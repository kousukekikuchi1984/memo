# Bluetooth Low Energy Central Side

# 実装方法

## 初期設定
service UUIDとcharacteristic UUIDをセットする、 `CBCentralManager` をinitializeする
```
self.serviceUUID = CBUUID.init(string: Constants.serviceUUID)
self.characteristicUUID = CBUUID.init(string: Constants.characteristicUUID)
self.centralManager = CBCentralManager.init(delegate: self, queue: nil)
```

## 端末がBluetoothを許可しているかなどの確認
`CBCentralManagerDelegate` の`centralManagerDidUpdateState` がBLEを使えるかどうか教えてくれる。そして、使える場合には、Service UUID, Characteristic UUID, 
```
    func centralManagerDidUpdateState(_ central: CBCentralManager) {
        if central.state == .poweredOn {
            // able to use BLE
        }
        // unable to use BLE
    }
 
```

## 近くにあるPeripheralをスキャンし、指定したCharcteristic UUIDがあるかどうかを調べる
接続先となるPeripheralを探す。これは `CBCentralManager` の `scanForPeripherals:withServives:options` を用いる。その際に、Service UUIDを渡す
```
self.centralManager.scanForPeripherals(withServices: [self.serviceUUID], options: nil)
```
見つかった場合は `centralManager(_:didDisconnectPeripheral:error:)` が呼ばれる。このperipheralに含まれるServices中に指定するCharacteristic UUIDがあるかどうかをperipheralに問い合わせる

```
func peripheral(_ peripheral: CBPeripheral, didDiscoverServices error: Error?) {
    if (error != nil) {
        // error
        return
    }
    if let services = peripheral.services {
        for service in services {
            self.peripheral.discoverCharacteristics([self.characteristicUUID], for: service)
        }
    }
}
```

## Characteristicから値を読み取る
Characteristicが見つかった場合、以下のメソッドが呼ばれる
```
peripheral:didDiscoverCharacteristicsForService:error()
```
その中でperipheralから送られて来たデータを `readValue:characteristic` を用いると `peripheral:didUpdateValueFor:error:` が呼ばれる。このデータ形式がData型なので、適切な型に変換してあげる
```
func peripheral(_ peripheral: CBPeripheral, didDiscoverCharacteristicsFor service: CBService, error: Error?) {
    if (error != nil) {
        // error
        return
    }
    if let characteristics = service.characteristics {
        for characteristic in characteristics {
            if characteristic.uuid.isEqual(self.characteristicUUID) {
                // read
                self.peripheral.readValue(for: characteristic)
            }
        }
    }
}

func peripheral(_ peripheral: CBPeripheral, didUpdateValueFor characteristic: CBCharacteristic, error: Error?) {
    if error != nil {
        // error
        return
    }
    let data = String.init(data: characteristic.value!, encoding: String.Encoding.utf8)
    self.addMessage(msg: data!)
}
```


## 検証方法
* iOS端末が複数ある場合
  * [LightBlue](https://itunes.apple.com/jp/app/lightblue/id639944780?mt=12)
* iOS端末が一つしかない場合
  * [Hardware IO Tools for XCode](https://developer.apple.com/download/more/)
  * このBluetooth Explorerが使える
