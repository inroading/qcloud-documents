## 1 Protocol Descriptions
<table style="display:table;width:100%">
  <tbody>
    <tr>
      <td style="width:15%;">
        Protocol
      </td>
      <td>
        HTTP POST
        <br />
      </td>
    </tr>
    <tr>
      <td>
        Encoding format
      </td>
      <td>
        UTF8
      </td>
    </tr>
    <tr>
      <td>
        URL
      </td>
      <td>
        For example: https://yun.tim.qq.com/v5/tlssmssvr/sendsms?sdkappid=xxxxx&random=xxxx
      </td>
    </tr>
    <tr>
      <td>
        API description
      </td>
      <td>
        Send the SMS verification code, SMS notification, marketing SMS to users (no more than 450 characters).
        <br />
        Note: Enter the applied SDKAppID as sdkappid, and a random number as random.
      </td>
    </tr>
  </tbody>
</table>

## 2 Request Packet
The packet is a JSON string with the following parameters:
```
{
    "tel": { //If you need to use the universal international phone number format, such as "+8613788888888", use the API "sendisms" (for more information, please see the following).
        "nationcode": "86", //Country code
        "mobile": "13788888888" //Mobile number
    }, 
    "sign": "Tencent Cloud ", //SMS signature. To use default signature, leave this field with the default value
    "tpl_id": 19, //Template ID approved on the console
     //Sample template: Your {1} is {2}. This verification code is valid for {3} minutes. If you are not using our service, ignore the message.
    "params": [
        "Verification code", 
        "1234", 
        "4"
    ], //Parameters, which correspond to {1}, {2}, and {3} in the above sample template
    "sig": "ecab4881ee80ad3d76bb1da68387428ca752eb885e52621a3129dcf4d9bc4fd4", //App credential. For more information on the calculation, please see the following
    "time": 1457336869, //UNIX timestamp, i.e. the time to initiate the request. A failure message will be returned if the time difference between the UNIX timestamp and the system time is greater than 10 minutes
    "extend": "", //The extended code of the channel (optional). Disabled by default. (A value must be specified.)
    //In the SMS reply scenario, the Tencent server returns it as is for developers to distinguish the specific reply type
    "ext": "" //User's session content (optional). The Tencent server returns it as is. You can leave it empty if it is not needed.
}
```
Notes:  
1. The approved template ID needs to be entered in the "tpl_id" field. Based on the combination of above request parameters, the delivered content is:  
"[Tencent Cloud] Your verification code is 1234. This verification code is valid in 4 minutes. If you are not using our service, ignore the message."   
If you have multiple SMS signatures, place the needed SMS signature in the "sign" field.  
For example, if you have two signatures of "[Tencent Technology]" and "[Tencent Cloud]", and you want to send an SMS message with "[Tencent Cloud]", the "sign" field can be "Tencent Cloud".    
2. To configure the "extend" field, [contact SMS Helper](/doc/product/382/3773).  
3. The API [sendisms](/document/product/382/8717). Enter the "tel" field in the universal international phone number format, such as "+8613788888888".  
4. The "sig" field is generated according to the formula sha256(appkey=$appkey&random=$random&time=$time&mobile=$mobile).  
The pseudo codes are as follows:
```
string strMobile = "13788888888"; //Content of the mobile field of tel
string strAppKey = "5f03a35d00ee52a21327ab048186a2c4"; //The corresponding appkey of sdkappid, which must be kept confidential.
string strRand = "7226249334"; //The value of the "random" field in the URL
string strTime = "1457336869"; //UNIX timestamp
string sig = sha256(appkey=5f03a35d00ee52a21327ab048186a2c4&random=7226249334&time=1457336869&mobile=13788888888)
           = ecab4881ee80ad3d76bb1da68387428ca752eb885e52621a3129dcf4d9bc4fd4;
```

## 3 Response Packet
```
{
    "result": 0, //0: Successful (the basis for billing). Other values: Failed
    "errmsg": "OK", //The specific error message when the "result" is not 0
    "ext": "", //User's session content. The Tencent server returns it as is.
    "sid": "xxxxxxx", //Indicate the ID of this delivery as well as an SMS delivery record
    "fee": 1 //Number of SMS messages billed
}
```
Notes:  
[Billing](/doc/product/382/3772#2-.E7.9F.AD.E4.BF.A1.E9.95.BF.E5.BA.A6)  
[Error Codes](/doc/product/382/3771)

## 4 SDK
We provide APIs of multiple platforms for developers to save the development time. Click [here](/doc/product/382/5804) to view details.
