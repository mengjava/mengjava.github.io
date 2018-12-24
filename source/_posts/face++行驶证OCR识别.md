---
title:face++行驶证OCR识别
date: 2018-12-17 21:32:24
categories: 
- Face++
tags:
- 行驶证OCR识别
- Face++
---

官方API地址为: https://console.faceplusplus.com.cn/documents/5671706
首先创建自己的账号 生成**api_key**  **api_secret** 
![请求参数](https://img-blog.csdnimg.cn/20181224171904900.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1bmd1YTgxNDQ=,size_16,color_FFFFFF,t_70)


![返回值说明](https://img-blog.csdnimg.cn/20181224172011968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1bmd1YTgxNDQ=,size_16,color_FFFFFF,t_70)
具体代码如下
```
public String ocrVehicleLicenseOrMergeFace(@RequestParam("imageUrl") String imageUrl,@RequestParam("group") String group,@RequestParam("type") Integer type) throws IOException {
		String image_url=FILE_INNER_URL+group+"/"+imageUrl;
		CloseableHttpClient httpClient = HttpClients.createDefault();
		HttpPost uploadFile = new HttpPost(LICENSE_URL);
		MultipartEntityBuilder builder = MultipartEntityBuilder.create();
		builder.addTextBody("api_key", API_KEY, ContentType.TEXT_PLAIN);
		builder.addTextBody("api_secret", API_SECRET, ContentType.TEXT_PLAIN);
		builder.addTextBody("image_url", image_url, ContentType.TEXT_PLAIN);
		// 把文件加到HTTP的post请求中
		HttpEntity multipart = builder.build();
		uploadFile.setEntity(multipart);
		CloseableHttpResponse  response = httpClient.execute(uploadFile);
		HttpEntity responseEntity = response.getEntity();
		//转换值
		String sResponse = EntityUtils.toString(responseEntity, "UTF-8");
		LicenseResult licenseResult = JSONObject.parseObject(sResponse, LicenseResult.class);
		return	JSONUtil.toJsonStringSUSSESS("查询成功", licenseResult);
	}
	```