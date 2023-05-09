---
title: Go实现封装HttpRequest方法
date: 2021-06-20
tags:
 - golang
categories:
 - 2021
---

```golang
package utils

import (
	"bytes"
	"encoding/json"
	"io/ioutil"
	"net/http"
	"time"
)

func HttpRequest(method string, url string, data interface{}, result interface{}, header map[string]interface{}, queryParams map[string]interface{}) (response *http.Response, resMap interface{}, err error) {
	var client = http.Client{
		Timeout: 3 * time.Second,
	}
	buf := bytes.NewBuffer(nil)
	encoder := json.NewEncoder(buf)
	if err = encoder.Encode(data); err != nil {
		return nil, nil, err
	}
	request, err := http.NewRequest(method, url, buf)
	if err != nil {
		return nil, nil, err
	}

	//request.Header.Add("Content-Type", "application/json")
	if header != nil {
		for k, v := range header {
			request.Header.Add(k, v.(string))
		}
	}
	if queryParams != nil {
		q := request.URL.Query()
		for k, v := range queryParams {
			q.Add(k, v.(string))
			request.URL.RawQuery = q.Encode()
		}
	}

	response, err = client.Do(request)
	if err != nil {
		return nil, nil, err
	}
	if result != nil {
		defer response.Body.Close()
		res, err := ioutil.ReadAll(response.Body)
		if err != nil {
			return nil, nil, err
		}
		if err = json.Unmarshal(res, &result); err != nil {
			return nil, nil, err
		}
		//err = json.NewDecoder(response.Body).Decode(result)
		return response, result, err
	}
	return
}

```