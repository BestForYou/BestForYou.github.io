---
layout: post
title: PHP并发执行－curl
subtitle: 
author: pizida
date: 2016-05-24 16:38:45 +0800
categories: 技术
tag: PHP杂项
---

#### PHP并行执行程序－－CURL
在程序中往往会有这样的场景，一个请求要展现数据，但是数据需要从很多个接口获取，比如5个接口拼装数据，每个接口需要0.2秒，5个需要1秒。
但是要是并行执行的话， 需要0.2秒就够了（最长时间执行的接口），
还有可能防止接口不通导致超时，漫长等待的结果。

```
 function rolling_curl($urls, $delay) {
        $queue = curl_multi_init();
        $map = array();

        foreach ($urls as $url) {
            $ch = curl_init();

            curl_setopt($ch, CURLOPT_URL, $url);
            curl_setopt($ch, CURLOPT_TIMEOUT, 5);
            curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
            curl_setopt($ch, CURLOPT_HEADER, 0);
            curl_setopt($ch, CURLOPT_NOSIGNAL, true);

            curl_multi_add_handle($queue, $ch);
            $map[(string) $ch] = $url;
        }

        $responses = array();
        do {
            while (($code = curl_multi_exec($queue, $active)) == CURLM_CALL_MULTI_PERFORM) ;

            if ($code != CURLM_OK) { break; }

            // a request was just completed -- find out which one
            while ($done = curl_multi_info_read($queue)) {

                // get the info and content returned on the request
                $info = curl_getinfo($done['handle']);
                $error = curl_error($done['handle']);
                $results = self::callback(curl_multi_getcontent($done['handle']), $delay);
                $responses[$map[(string) $done['handle']]] = compact('info', 'error', 'results');

                // remove the curl handle that just completed
                curl_multi_remove_handle($queue, $done['handle']);
                curl_close($done['handle']);
            }

            // Block for data in / output; error handling is done by curl_multi_exec
            if ($active > 0) {
                curl_multi_select($queue, 0.5);
            }

        } while ($active);

        curl_multi_close($queue);
        return $responses;
    }

	//用于处理返回的数据
    function callback($data, $delay) {
        preg_match_all('/<h3>(.+)<\/h3>/iU', $data, $matches);
        usleep($delay);
        return compact('data');
    }
```