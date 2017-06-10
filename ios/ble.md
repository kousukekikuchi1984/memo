# Bluetooth Low Energy 

## 検証方法
どれらの方法も基本的にはadvertisingができないものであるので、できる限りiOS端末を用いる方が良い
* iOS端末が複数ある場合
  * [LightBlue](https://itunes.apple.com/jp/app/lightblue/id639944780?mt=12)
* iOS端末が一つしかない場合
  * [Hardware IO Tools for XCode](https://developer.apple.com/download/more/)
  * このBluetooth Explorerが使える
* Android端末がある場合
  * [nRF Connect](https://play.google.com/store/apps/details?id=no.nordicsemi.android.mcp&hl=en)
  * 端末依存でAdvertisingが使えない場合があるので注意する必要がある
  * 基本は以下の3つの関数を使っているとのことだが、それでも接続できない場合がある。
```
package com.example.owner.advertising

import android.bluetooth.BluetoothAdapter
import android.support.v7.app.AppCompatActivity
import android.os.Bundle
import android.bluetooth.BluetoothManager

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        var adaper = BluetoothAdapter.getDefaultAdapter()
        println(adaper.isMultipleAdvertisementSupported)
        println(adaper.isOffloadedScanBatchingSupported)
        println(adaper.isOffloadedFilteringSupported)
        super.onCreate(savedInstanceState)

        setContentView(R.layout.activity_main)
    }
}
```

