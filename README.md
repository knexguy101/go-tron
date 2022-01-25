# Go-Tron

A Tron API implementation written in Go

## Notice

This API is currently in alpha and it is not recommended it be used in production. __Use this at your own risk.__ When the API is stablized this notice will be removed and an issue will be filed indicating that it is safe to use. This will be done when test coverage hits a healthy percentage and the API has matured a bit.

## Examples

### Transfer

```go
package main

import (
	"github.com/0x10f/go-tron/account"
	"github.com/0x10f/go-tron/client"
	"log"
)

const (
  	srcPrivKey  = "000000000000000000000000000000000000000000000000000000000000010f"
  	destPrivKey = "000000000000000000000000000000000000000000000000000000000000010e"
)
  
func main() {
	src, err := account.FromPrivateKeyHex(srcPrivKey)
	if err != nil {
		log.Fatal("Failed to parse private key hex - ", err)
	}

	dest, err := account.FromPrivateKeyHex(destPrivKey)
	if err != nil { 
		log.Fatal("Failed to parse private key hex - ", err)
	}

	cli := client.New("http://127.0.0.1:16667", "TRON-API-KEY-GOES-HERE")

	tx, err := cli.Transfer(src, dest.Address(), 1000000 /* in sun */)
	if err != nil {
		log.Fatal("Failed to transfer tron balance - ", err)
	}

	log.Printf("%#v\n", tx)
}
```

### Transfer USDT TRC20

```go
import (
	"github.com/joho/godotenv"
	"github.com/knexguy101/go-tron/abi"
	"github.com/knexguy101/go-tron/account"
	"github.com/knexguy101/go-tron/address"
	"github.com/knexguy101/go-tron/client"
	"os"	
)

const (
	usdtContract = "TR7NHqjeKQxGTCi8q8ZY4pL8otSzgjLj6t"
)

tronClient = client.New("https://api.trongrid.io", "GRID_API_KEY")
to := "TX...."
var amount uint64 = 1

storageAccount, err := account.FromPrivateKeyHex("SENDERS PRIVATE KEY")
if err != nil {
	panic(err)
}

inputAddr, err := address.FromBase58(toAddress)
if err != nil {
	return err
}

contractAddr, err := address.FromBase58(usdtContract)
if err != nil {
	return err
}

res, err := tronClient.CallContract(storageAccount, client.CallContractInput{
	Function: abi.Function{
		Mutability: "",
		Name: "transfer",
		Inputs: []abi.Value {
			{
				Name: "to",
				Type: abi.TypeAddress,
			},
			{
				Name: "value",
				Type: abi.TypeUint256,
			},
		},
	},
	Address: contractAddr,
	Arguments: []interface{}{
		inputAddr,
		amount,
	},
	FeeLimit: 1e10,
})
if err != nil {
	return err
}
	
if err := tronClient.BroadcastTransaction(&res); err != nil {
	return err
}

txId := res.Id
tronClient.Await(txId, 10 * time.Second)
```
