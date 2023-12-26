# API

The scripts can be used as [Tasks](broken-reference) and [Alerts](broken-reference), you can also use API to integrate the script result in your service. &#x20;

## Get Script: /scripts/${scriptId}

```bash
curl --request GET --url https://compassdao.com/i/scripts/0x2a03cd198ab14df0
```

Response:

```json
{
	"result": {
		"id": "0x2a03cd198ab14df0",
		"owner": "0x81b607b86ddf539d46301dd71cff16ddfa6b18e1",
		"code": "\nexport const name = \"My Awesome Script\"\nexport const description = \"Get balance of address\"\nexport const tag = \"eth balance\"\n\nconst formatBalance = (wei: ethers.BigNumber) =>\n  Number(Number(ethers.utils.formatEther(wei)).toFixed(4))\n\nexport const run = async ({ address }: Record<string, string>) => {\n  const provider = sdk.getDefaultProvider(\"ethereum\")\n  const balance = await provider.getBalance(address)\n\n  return {\n    address,\n    balance: formatBalance(balance),\n  }\n}\n",
		"createdAt": 1659938040107,
		"updatedAt": 1659938040107,
		"userId": 4,
		"meta": {
			"id": 114,
			"scriptId": "0x2a03cd198ab14df0",
			"name": "My Awesome Script",
			"description": "Get balance of address",
			"tag": "eth balance",
			"createdAt": 1659938040107,
			"updatedAt": 1659938040107,
			"args": "address"
		}
	}
}
```

## Run Script: /scripts/${scriptId}/run

```shell
curl --request POST \
  --url https://compassdao.com/i/scripts/0x2a03cd198ab14df0/run \
  --header 'Content-Type: application/json' \
  --data '{
	"params": "{\"address\":\"0xd8da6bf26964af9d7eed9e03e53415d37aa96045\"}"
}'
```

Response:

```json
{
	"result": {
		"scriptId": "0x2a03cd198ab14df0",
		"timeUsed": 672,
		"result": {
			"status": "fulfilled",
			"value": {
				"address": "0xd8da6bf26964af9d7eed9e03e53415d37aa96045",
				"balance": 1883.4684
			}
		}
	}
}
```
