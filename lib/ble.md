# Bluetooth(BLE) 통신 구현

## 기본 개념
* Central: 일종의 서버 역할.
* Peripheral: 주변기기.

Central에 여러개의 Peripheral이 붙을 수 있는 구조.

### iOS(Central) & Android(Peripheral)
#### iOS

```swift
import Foundation
import CoreBluetooth

class BleCentral : NSObject, CBCentralManagerDelegate, CBPeripheralDelegate {
  
  private static var instacne: BleCentral! = nil
  static var shared: BleCentral {
    if instacne == nil  {
      instacne = BleCentral()
    }
    return instacne
  }
  
    private var centralManager: CBCentralManager!
    private var myPeripheral: CBPeripheral!
    private var serviceArray = [CBService]()
    private var characteristicArray = [CBCharacteristic]()
  
  var isConnected: Bool {
    get {
      return centralManager != nil && myPeripheral != nil && !serviceArray.isEmpty && !characteristicArray.isEmpty
    }
  }
  
  override init() {
    super.init()
  }
  
    func initilaize() {
        self.centralManager = CBCentralManager(delegate: self , queue: nil)
    }
  
  func startScan() {
    guard let central = self.centralManager else { return }
    startScan(central: central)
  }
  
  private func startScan(central: CBCentralManager) {
    let services = [UUID_SERVICE]
    central.scanForPeripherals(withServices: services, options: nil)
  }
  
  func scanService() {
    guard let peripheral = self.myPeripheral else { return }
    scanService(peripheral: peripheral)
  }
  
  private func scanService(peripheral: CBPeripheral) {
    let services = [UUID_SERVICE]
    peripheral.discoverServices(services)
  }
  
    func stopScan() {
        self.centralManager.stopScan()
    }
  
  func getCurrLogDirPath() -> String {
    guard let dirName = getLogDirName(self.currentLog?.startDate) else {
      return ""
    }
    
    return getLogDirUrl().appendingPathComponent(dirName).path
  }
  
  //---- CBCentralManagerDelegate ------------------------------------------------------------------------------------
    
    func centralManagerDidUpdateState(_ central: CBCentralManager) {
        switch (central.state) {
        case .poweredOff:
            print("BLE PoweredOff")
        case .poweredOn:
            print("BLE PoweredOn")
      self.delegate?.onBlePowerOn()
      //startScan()
        case .resetting:
            print("BLE Resetting")
        case .unauthorized:
            print("BLE Unauthorized")
        case .unknown:
            print("BLE Unknown")
        case .unsupported:
            print("BLE Unsupported")
        }
    }
  
    func centralManager(_ central: CBCentralManager, didDiscover peripheral: CBPeripheral, advertisementData: [String:Any], rssi RSSI: NSNumber) {
        self.myPeripheral = peripheral
        self.centralManager.connect(peripheral, options: nil)
    stopScan()
    }
    
    func centralManager(_ central: CBCentralManager, didConnect peripheral: CBPeripheral) {
    // 特定のデバイス(TARGET_WATCH_DEVICE)だけと通信する場合
    guard peripheral.name?.starts(with: TARGET_WATCH_DEVICE) == true else { return }
    
        peripheral.delegate = self
    if self.myPeripheral == nil {
      self.myPeripheral = peripheral
    }
    
    scanService(peripheral: peripheral)
    }
  
    func centralManager(_ central: CBCentralManager, didFailToConnect peripheral: CBPeripheral, error: Error?) {
    print(error.debugDescription)
    guard peripheral.name?.starts(with: TARGET_WATCH_DEVICE) == true else { return }
    
    self.delegate?.onConnectFailed()
    }
  
  
  //---- CBPeripheralDelegate ----------------------------------------------------------------------------------------
  
    func peripheral(_ peripheral: CBPeripheral, didDiscoverServices error: Error?) {
    print("検索されたサービス: \(peripheral.services?.count ?? 0)")
    self.delegate?.onConnected(peripheral: peripheral)
    
    guard error == nil else {
      print("error: \(error!.localizedDescription)")
      return
    }
    
    self.serviceArray.removeAll()
    if let services = peripheral.services {
      for service in services {
        self.serviceArray.append(service)
        
        print("search: Characteristic")
        let chars = [UUID_CHAR]
        peripheral.discoverCharacteristics(chars, for: service)
      }
    }
    }
  
    func peripheral(_ peripheral: CBPeripheral, didDiscoverCharacteristicsFor service: CBService, error: Error?) {
    guard error == nil else {
      print("error: \(String(describing: error))")
      return
    }
    
    print("didDiscoverCharacteristicsFor service:\(service.uuid)")
    
    if let characteristics = service.characteristics {
      for characteristic in characteristics {
        self.characteristicArray.append(characteristic)
        
        print("found characteristic: \(characteristic.uuid)")
        peripheral.setNotifyValue(true, for: characteristic)
      }
    }
    }
  
    func peripheral(_ peripheral: CBPeripheral, didUpdateValueFor characteristic: CBCharacteristic, error: Error?) {
    guard error == nil else {
      print("エラー：\(error!.localizedDescription)")
      return
    }
    
    guard let value = characteristic.value else {
      print("エラー：データが空")
      return
    }
    
        value.withUnsafeBytes {
          // Data.init(bytes: $0, ...
        }
    }
  
    func peripheral(_ peripheral: CBPeripheral, didModifyServices invalidatedServices: [CBService]) {
    self.serviceArray.removeAll { service in
      invalidatedServices.contains(service)
    }
    print("invalidatedServices")
    scanService()
    }
  
    func peripheral(_ peripheral: CBPeripheral, didUpdateNotificationStateFor characteristic: CBCharacteristic, error: Error?) {
        if let error = error {
    print("error: \(error)")
        }
        else {
    print("isNotifying: \(characteristic.isNotifying)")
        }
        print("characteristic: \(characteristic.uuid)")
    }
  
  
  //---- Peripheral Utilities ----------------------------------------------------------------------------------------
  
  private func requestRead(_ peripheral: CBPeripheral, characteristic: CBCharacteristic) {
    peripheral.readValue(for: characteristic)
  }
  
  private func sendTo(_ peripheral: CBPeripheral, characteristic: CBCharacteristic, data: Data) {
    peripheral.writeValue(data, for: characteristic, type: .withResponse)
  }
  
  func sendData(string: String) -> Bool {
    guard let peripheral = self.myPeripheral else {
      return false
    }
    
    guard self.isConnected else {
      return false
    }
    
    for char in self.characteristicArray {
      if let data = string.data(using: .utf8) {
        sendTo(peripheral, characteristic: char, data: data)
      }
    }
    return !self.characteristicArray.isEmpty
  }
}
```

#### Android(Wear OS)
```kotlin
import android.bluetooth.*
import android.bluetooth.le.AdvertiseCallback
import android.bluetooth.le.AdvertiseData
import android.bluetooth.le.AdvertiseSettings
import android.content.Context
import android.os.ParcelUuid
import android.util.Log
import java.util.*

class BleService(private val context: Context) {

    private val bluetoothManager
        get() = context.getSystemService(BluetoothManager::class.java)

    private var gattServer: BluetoothGattServer? = null
    private var device: BluetoothDevice? = null
    private var deviceAddress: String? = null
    private var characteristic: BluetoothGattCharacteristic? = null

    // BLE通信関連のイベント
    interface IBleServiceEventListener {
        fun onBleStarted() // BLE Serviceが開始した時
        fun onBleStartFailed() // BLE Serviceが開始できなかった時
        fun onReadRequest(): ByteArray // iPhoneアプリから「READ」要請が届いた時
        fun onWriteRequest(text: String): ByteArray? // iPhoneアプリから「WRITE」要請が届いた時
        fun onSendFailed() // iPhoneアプリへデータを送信しようとしたが、失敗した時
    }

    var eventListener: IBleServiceEventListener? = null

    // BLE通信の準備ができたら「true」
    val isReady: Boolean
        get() = this.device != null && !this.deviceAddress.isNullOrEmpty()

    fun startBleService() {
        val settings = AdvertiseSettings.Builder().apply {
            setAdvertiseMode(AdvertiseSettings.ADVERTISE_MODE_LOW_POWER)
            setConnectable(true)
            setTimeout(0)
            setTxPowerLevel(AdvertiseSettings.ADVERTISE_TX_POWER_LOW)
        }.build()

        val data = AdvertiseData.Builder().apply {
            addServiceUuid(ParcelUuid(UUID.fromString(UUID_SERVICE)))
        }.build()

        val adapter = this.bluetoothManager.adapter
        adapter.bluetoothLeAdvertiser.apply {
            initBluetoothGattServer()
            startAdvertising(settings, data, object: AdvertiseCallback() {
                override fun onStartSuccess(settingsInEffect: AdvertiseSettings?) {
                    eventListener?.onBleStarted()
                }
                override fun onStartFailure(errorCode: Int) {
                    eventListener?.onBleStartFailed()
                }
            })
        }
    }

    private fun initBluetoothGattServer() {
        val self = this

        this.gattServer = this.bluetoothManager.openGattServer(context.applicationContext, object : BluetoothGattServerCallback() {
            override fun onCharacteristicReadRequest(
                device: BluetoothDevice?,
                requestId: Int,
                offset: Int,
                characteristic: BluetoothGattCharacteristic?
            ) {
                super.onCharacteristicReadRequest(device, requestId, offset, characteristic)

                val data = eventListener?.onReadRequest()
                if (data?.size ?: 0 > 0) {
                    val result = gattServer?.sendResponse(device, requestId, BluetoothGatt.GATT_SUCCESS, offset, data)
                    if (result != true) {
                        eventListener?.onSendFailed()
                        return
                    }
                }
            }

            override fun onCharacteristicWriteRequest(
                device: BluetoothDevice?,
                requestId: Int,
                characteristic: BluetoothGattCharacteristic?,
                preparedWrite: Boolean,
                responseNeeded: Boolean,
                offset: Int,
                value: ByteArray?
            ) {
                super.onCharacteristicWriteRequest(
                    device,
                    requestId,
                    characteristic,
                    preparedWrite,
                    responseNeeded,
                    offset,
                    value
                )
                var responseData: ByteArray? = null

                if (value != null && value.isNotEmpty()) {
                    val text = String(value)
                    responseData = eventListener?.onWriteRequest(text)
                }

                if (responseNeeded) {
                    val result = gattServer?.sendResponse(device, requestId, BluetoothGatt.GATT_SUCCESS, offset, responseData ?: "".toByteArray())
                    if (result != true) {
                        eventListener?.onSendFailed()
                    }
                }
            }

            override fun onConnectionStateChange(device: BluetoothDevice?, status: Int, newState: Int) {
                super.onConnectionStateChange(device, status, newState)
                when (newState) {
                    BluetoothProfile.STATE_CONNECTED -> {
                        self.device = device
                        self.deviceAddress = device?.address
                    }
                    BluetoothProfile.STATE_DISCONNECTED -> {
                        self.device = BluetoothAdapter.getDefaultAdapter().getRemoteDevice(self.deviceAddress)
                    }
                }
            }
        })

        val service = BluetoothGattService(
            UUID.fromString(UUID_SERVICE),
            BluetoothGattService.SERVICE_TYPE_PRIMARY
        )

        this.characteristic = BluetoothGattCharacteristic(
            UUID.fromString(UUID_CHAR),
            BluetoothGattCharacteristic.PROPERTY_NOTIFY
                .or(BluetoothGattCharacteristic.PROPERTY_READ)
                .or(BluetoothGattCharacteristic.PROPERTY_WRITE)
                .or(BluetoothGattCharacteristic.PROPERTY_INDICATE)
                .or(BluetoothGattCharacteristic.PROPERTY_WRITE_NO_RESPONSE),
            BluetoothGattCharacteristic.PERMISSION_READ
                .or(BluetoothGattCharacteristic.PERMISSION_WRITE)
        )

        service.addCharacteristic(this.characteristic)
        this.gattServer?.addService(service)
    }

    fun stopBleService() {
        this.gattServer?.close()
        this.gattServer = null
        this.device = null
        this.deviceAddress = null
        this.characteristic = null
    }

    fun sendNotiString(data: String): Boolean {
        if (!isReady) return false

        this.characteristic?.setValue(data)
        val result = this.gattServer?.notifyCharacteristicChanged(this.device, this.characteristic, false) == true
        if (!result) {
            eventListener?.onSendFailed()
        }

        return result
    }

    fun sendNotiBytes(data: ByteArray): Boolean {
        this.characteristic?.value = data
        val result = this.gattServer?.notifyCharacteristicChanged(this.device, this.characteristic, false) == true
        if (!result) {
            eventListener?.onSendFailed()
        }

        return result
    }

}
```

