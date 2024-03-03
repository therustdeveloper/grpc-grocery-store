# gRPC Grocery Store

## Create the Service Definition Protobuf

Create the `proto` directory and the service definition, in this case `proto/store.proto`.

```shell
mkdir proto/
touch proto/store.proto
```

## Create the build.rs code

Create the `build.rs` file in the root of the project and run the following command to generate the `store.rs` file in the `src/` directory.

```shell
cargo build
```

## Testing gRPC Server

```shell
cargo run --release --bin server
```

## Testing gRPC Client

```shell
cargo build --release --bin cli
```

### Copy the gRPC Client

```shell
cp target/release/cli ./
```

### Add an Item

```shell
./cli add --sku TESTSKU --price 1.99 --quantity 20 --name bananas --description "yellow fruit"
success: item was added to the inventory.
```

### Retrieve the Item

```shell
./cli get --sku TESTSKU                                                                                                                                                     ─╯
found item: Item { identifier: Some(ItemIdentifier { sku: "TESTSKU" }), stock: Some(ItemStock { price: 1.99, quantity: 20 }), information: Some(ItemInformation { name: Some("bananas"), description: Some("yellow fruit") }) }
```

### Rejects the duplicate

```shell
./cli add --sku TESTSKU --price 2.99                                                                                                                                        ─╯
Error: Status { code: AlreadyExists, message: "item already exists in inventory", metadata: MetadataMap { headers: {"content-type": "application/grpc", "date": "Sun, 03 Mar 2024 20:15:09 GMT", "content-length": "0"} }, source: None }
```

### Change the quantity of an Item

```shell
./cli update-quantity --sku TESTSKU --change +50                                                                                                                            ─╯
success: quantity was updated. Quantity: 70 Price: 1.99
```

### Update the price

```shell
./cli update-price --sku TESTSKU --price 2.99                                                                                                                               ─╯
success: price was updated. Quantity 70 Price: 2.99
```

#### The Item is already in that price

```shell
./cli update-price --sku TESTSKU --price 2.99                                                                                                                               ─╯
Error: Status { code: InvalidArgument, message: "item is already at this price", metadata: MetadataMap { headers: {"content-type": "application/grpc", "date": "Sun, 03 Mar 2024 20:16:31 GMT", "content-length": "0"} }, source: None }
```

## Watch the Item

In another terminal watch the item. And run commands against the Item in the previous terminal.

```shell
./cli watch --sku TESTSKU                                                                                                                                                   ─╯
streaming changes to item TESTSKU
item was updated: Item { identifier: Some(ItemIdentifier { sku: "TESTSKU" }), stock: Some(ItemStock { price: 2.19, quantity: 53 }), information: Some(ItemInformation { name: Some("bananas"), description: Some("yellow fruit") }) }
item was updated: Item { identifier: Some(ItemIdentifier { sku: "TESTSKU" }), stock: Some(ItemStock { price: 1.99, quantity: 53 }), information: Some(ItemInformation { name: Some("bananas"), description: Some("yellow fruit") }) }
watched item has been removed from the inventory.
stream closed
```


