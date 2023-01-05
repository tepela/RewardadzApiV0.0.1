# Pay with Rewardadz api - Beta

- to get started create an account at [Rewardadz](https://live.rewardadz.com/auth/registration)
- once logged in navigate to settings > api and get your api key

### Available Apis(Request and Responses)
1: Initiating payement api (`{{base_url}}/app/ra/v1/api/pay/with/ra`) `POST`
<br/>
This above api requires the following parameters
<br />
- **BODY PARAMS**
  - Phone number of the user `string` example `"+254700000000"`
  - Amount the user wants to pay `number` exampale `100`
  - callback url `string`, this is where the data will be send when transaction is succesful example `https://yourapi.api/test/callback`
- **HEADERS**
  - Your api key `ra-api-key` of type `string`
  
#### example
```js
import express, { Request, Response } from "express";

const app = express();

app.use(express.json());

app.post("/pay", async (req: Request, res: Response): Promise<object> => {
  const { phone, amount, call_back_url } = req.body;
  const api_key = "xxxxxxxxxxxxxxxxxxxxxxxx";
  try {
    const pay: any = await pay_with_ra(phone, amount, call_back_url, api_key);
    if (pay.data !== undefined && pay.data.success === true)
      // do something with the response
      return res.status(200).json(pay.data);
    res.status(400).json(pay.response.data);
    return;
  } catch (error) {
    res.status(500).json(error);
    return;
  }
});
//pay with ra helper function
async function pay_with_ra(
  phone: string,
  amount: number,
  call_back_url: string,
  api_key: string
): Promise<object> {
  const data = { phone, amount, call_back_url };
  const headers = { "ra-api-key": api_key };
  try {
    const pay = await axios.post(
      `${base_url}` + "/app/ra/v1/api/pay/with/ra",
      data,
      { headers }
    );
    return pay;
  } catch (error) {
    return error;
  }
}
  ```
 - ***Responses***
    - Invalid API key - `403`
       - Sample 
           ```json
           {
              "success": false,
              "message": "Invalid api key",
              "data": {
                  "code": 403,
                  "note": "Provide a valid key"
              }
            }
            ```
     - User not Found `400`
         - sample
            ```json
              {
                "success": false,
                "message": "User Does not exist",
                "data": {
                    "code": 404,
                    "note": "The user must registered on Rewardadz"
                }
            }
            ```
    - Insuficient balance `400`
       - sample
            ```json
              {
                "success": false,
                "message": "Insufficient balance",
                "data": {
                  "code": 51,
                   "note": "Account has no funds"
                 }
              }
            ```
     - Numbers less than 0 `400`
        - sample
            ```json
             {
              "success": false,
              "message": "Invalid number",
              "data": {
                  "code": 12,
                  "note": "Transaction declined."
              }
            }
            ```
      -   payment initiated sucessfully `200`
        - sample
            ```json
            {
                "success": true,
                "message": "Payment initiated",
                "data": null
            }
            ```

2: Confirming payments API (`{{base_url}}/app/ra/v1/api/confirm/payments`) `POST`
  <br />
  The above api take the following parameters
  <br/>
  ***BODY PARAM***
  - Phone number of the user `string` example `"+254700000000"`
  - OTP which is send to the user phone number `number` example  `123456`
  </br>
  
  ### example
  ```js
    app.post("/confirm", async (req: Request, res: Response): Promise<object> => {
    const { phone, otp } = req.body;
    try {
      const confirm: any = await confirm_payment(phone, otp);
      if (confirm.data === undefined) {
        return res.status(400).json(confirm.response.data);
      }
      return res.status(200).json(confirm.data);
    } catch (error) {
      res.status(500).json(error);
      return;
    }
  });
  //confirm payements helper function
  async function confirm_payment(
  phone: string,
  otp: number
): Promise<object> {
  const data = { phone, otp };
  try {
    const confirm = await axios.post(
      `${base_url}` + "/app/ra/v1/api/confirm/payments",
      data
    );
    return confirm;
  } catch (error) {
    return error;
   }
}
```
***Responses***
- Invalid OTP `403`
  - sample
    ```json
     {
        "success": false,
        "message": "Invalid OTP",
        "data": {
            "code": 403,
            "note": "The code provided is incorect"
        }
    }
    ```
 - Valid OTP `200`
   - sample
     ```json
       {
          "sucess": true,
          "message": "Transaction confirmed",
          "data": {
              "code": 2001,
              "note": "Payement is being processed"
          }
      }
     ```
Then the transaction data is send to the callback url provided when initiating the payements
<br/>
below is the example of a callback route

```js
 app.post("/callback", (req: Request, res: Response): void => {
   const data = req.body
   // do something with the data
 });
```
