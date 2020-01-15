# Mockito的使用及原理及分析

一次完整的Mock包括：
- 设定目标
- 设置消费条件
- 预期返回结果
- 消费并检验返回结果

	when(productService.getProductInfo(any())).
		thenAnswer(invocationOnMock -> {
			List<ProductInfo> productInfos = new ArrayList<>();
			return productInfos;
		});
    
    //...        
    List<ProductInfo> productInfos = productService.getProductInfo(1);

- 设定目标 -> List<ProductInfo> productInfos
- 设置消费条件 -> productService.getProductInfo(any())
- 预期返回结果 -> thenAnswer(...)
- 消费并检验返回结果 -> productService.getProductInfo(1)

Mockito主要就是通过Stub打桩，通过方法名加参数来准确的定位测试桩然后返回预期的值。

