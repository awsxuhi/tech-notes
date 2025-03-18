

BGP Routing

MED-Multi-Exit Discriminator

MED用来解决在下图中R1-R3之间两条链路的选择。这个情况下如果使用AS_PATH那么当100M的链路断开的时候，因为R1-R3之家的这条20M链路AS_PATH比R1-R2和R1-R4之间的要多一跳，造成路由就不选择R3，这是不想发生的。所以这个时候R1-R3之间保持AS_PATH一样，改用MED来选择线路。MED小的优先，这个和Weight/Local Preference正好相反，后者是越大越优先。

![image-20191110205105505](/Users/xuhi/Library/Application Support/typora-user-images/image-20191110205105505.png)