



We need to add below headers in the response of lambda functions to let the other domain site could fetch the data.

```javascript
 headers: {
      "Access-Control-Allow-Origin": "*",
      "Access-Control-Allow-Headers": "*"
    },
```



the lambda:

```javascript
var handler = async (event = {}) => {
  console.log(event);
  return {
    statusCode: 201,
    headers: {
      "Access-Control-Allow-Origin": "*",
      "Access-Control-Allow-Headers": "*"
    },
    body: JSON.stringify({
      name: "xuhi",
      company: "amazon company"})
  };
};
```

The testing result shows, we 100% need to add cors header in the response in the lambda functions. It doesn't matter whether we use proxy or non-proxy mode.



手动修改api gateway，让get method加上response headers，发现虽然返回成功，但是里面没有需要lambda返回的json的值。