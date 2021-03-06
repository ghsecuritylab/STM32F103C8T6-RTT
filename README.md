# 基于STM32 rsa lib库实现rsa的数字签名

### 服务器端：

- 生成1024位的rsa密钥对

  ```
  openssl genrsa -out rsaprivate.pem 1024 
  openssl rsa -in rsaprivate.pem  -pubout -out rsapublic.pem
  ```

- 使用asn1parse指令查看rsa密钥的16进制，将16进制的模数n使用base64进行编码，并以UTF-8标准转化成字符串，发送给STM32,公钥指数e默认010001

  ```
  openssl asn1parse -in rsaprivate.pem
  ```

  ![Image](https://github.com/xaodongdong/STM32F103C8T6-RTT/blob/rsa-lib/Image.png )

  

- 将重要数据data.txt使用sha1 和 rsa私钥生成对应的数字签名rsasign.bin

  ```
  openssl sha1 -sign rsaprivate.pem -out rsasign.bin data.txt
  ```

- 将数据data.txt和数字签名rsasign.bin使用base64进行编码，并以UTF-8标准转化成字符串，发送给STM32

- 服务器可以用sha1算法验证data.txt的数字签名rsasign.bin，验证的public key为RSA算法生成的rsapublic.pem。

  ```
  openssl sha1 -verify rsapublic.pem -signature rsasign.bin file.txt
  ```

  

### STM32端: 

- 将客户端发送来的字符串公钥（模数）转换成16进制存储起来
- 收到重要数据和相应的数字签名后使用sha1 和 rsa公钥对数据进行校验
- 

