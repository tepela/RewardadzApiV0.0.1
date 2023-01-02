# Pay with Rewardadz api - Beta

- to get started create an account at https://live.rewardadz.com/auth/registration
- once logged in navigate to settings > api and get your api key

### intergration sample with nodejs 
for a transaction to be initiated the user making the request must have an account with rewardadz and enough balance
<br />
this is an example of payement initiation with nodejs and typescript
```js
import axios from "axios";

const base_url = "";

export default async function pay_with_ra(
  phone: string,
  amount: number,
  callback_url: string,
  api_key: string
): Promise<object> {
  const data = { phone, amount, callback_url };
  const headers = { "ra-api-key": api_key };
  try {
    const pay = await axios.post(
      `${base_url}` + "/ra/v1/api/pay/with/ra",
      data,
      { headers }
    );
    const res = pay.data;
    return res;
  } catch (error) {
    return error;
  }
}
```
- this is an sample of your route

```js
import express, { Request, Response } from "express";
import pay_with_ra from ".";

const app = express();

app.use(express.json());

app.post("/pay", async (req: Request, res: Response): Promise<object> => {
  const { phone, amount, callback_url } = req.body;
  const api_key = "";
  try {
    const pay = await pay_with_ra(phone, amount, callback_url, api_key);
    console.log(pay);
  } catch (error) {
    res.status(500).json(error);
    return;
  }
});
```
