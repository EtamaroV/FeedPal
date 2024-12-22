<script setup>
import TextLogo from '@/components/icons/TextLogo.vue';

import mqtt from 'mqtt';

</script>

<template>
  <div class="main">

    <v-dialog v-model="isActiveAddNewPage" max-width="500">
    
      <template v-slot:default>
        <v-card title="เพิ่มอุปกรณ์ใหม่">
          <v-card-text>

            

            <v-text-field v-if="!isActiveAddNewPageNext"
              v-model="AddNewDeviceData.DeviceName"
              label="ชื่ออุปกรณ์"
              :rules="[AddNewDeviceData.idform.rules.required]"
              required
            ></v-text-field>

            <v-divider v-if="!isActiveAddNewPageNext"></v-divider>
            <br>

            <small v-if="!isActiveAddNewPageNext" class="text-caption text-medium-emphasis">โปรดตั้งไอดีและรหัสผ่านให้กับอุปกรณ์ของคุณ</small>
            <v-text-field v-if="!isActiveAddNewPageNext"
              v-model="AddNewDeviceData.DeviceId"
              :rules="[AddNewDeviceData.idform.rules.required, AddNewDeviceData.idform.rules.max]"
              label="ไอดีอุปกรณ์"
              counter="100"
              required
            ></v-text-field>
            <v-text-field v-if="!isActiveAddNewPageNext"
              v-model="AddNewDeviceData.password"
              :rules="[AddNewDeviceData.pwform.rules.required, AddNewDeviceData.pwform.rules.min]"
              :type="AddNewDeviceData.pwform.show ? 'text' : 'password'"
              hint="ขั้นต่ำ 8 ตัวอักษร"
              label="รหัสผ่าน"
              counter
              required
            ></v-text-field>

            <small v-if="isActiveAddNewPageNext" class="text-caption text-medium-emphasis">นำ Token ไปใส่ในโค้ดของคุณ</small>
            <v-text-field v-if="isActiveAddNewPageNext"
              label="Token"
              :modelValue="AddNewDeviceData.token"

              readonly
            ></v-text-field>
          </v-card-text>
        
          <v-card-actions>
            <v-spacer></v-spacer>
          
            <v-btn v-if="isActiveAddNewPageNext"
              text="ปิด"
              @click="isActiveAddNewPage = false; this.mqttclient.publish(AddNewDeviceData.token + '/Device', 'HEARTBEAT');"
            ></v-btn>

            <v-btn v-if="!isActiveAddNewPageNext"
              text="ยกเลิก"
              @click="isActiveAddNewPage = false"
            ></v-btn>

            <v-btn
              v-if="!isActiveAddNewPageNext"
              :disabled="(AddNewDeviceData.DeviceName.length <= 0) || (AddNewDeviceData.DeviceId.length <= 0) || (AddNewDeviceData.DeviceId.length > 100) || (AddNewDeviceData.password.length < 8)"
              color="var(--color-theme-text)"
              text="ถัดไป"
              variant="tonal"
              @click="isActiveAddNewPageNext = true; genToken(); dataStoreAddNew();"
            ></v-btn>

          </v-card-actions>
        </v-card>
      </template>
    </v-dialog>

    <v-dialog v-model="isActiveAddExistPage" max-width="500">
    
      <template v-slot:default>
        <v-card title="เพิ่มอุปกรณ์ที่มีอยู่">
          <v-card-text>
            <v-text-field
              v-model="AddNewDeviceData.DeviceName"
              label="ชื่ออุปกรณ์"
              :rules="[AddNewDeviceData.idform.rules.required]"
              required
            ></v-text-field>

            <v-divider></v-divider>
            <br>

            <v-text-field 
              v-model="AddNewDeviceData.DeviceId"
              :rules="[AddNewDeviceData.idform.rules.required, AddNewDeviceData.idform.rules.max]"
              label="ไอดีอุปกรณ์"
              counter="100"
              required
            ></v-text-field>
            <v-text-field 
              v-model="AddNewDeviceData.password"
              :rules="[AddNewDeviceData.pwform.rules.required, AddNewDeviceData.pwform.rules.min]"
              :type="AddNewDeviceData.pwform.show ? 'text' : 'password'"
              hint="ขั้นต่ำ 8 ตัวอักษร"
              label="รหัสผ่าน"
              counter
              required
            ></v-text-field>
          </v-card-text>
        
          <v-card-actions>
            <v-spacer></v-spacer>

            <v-btn
              text="ยกเลิก"
              @click="isActiveAddExistPage = false"
            ></v-btn>

            <v-btn
              :disabled="(AddNewDeviceData.DeviceName.length <= 0) || (AddNewDeviceData.DeviceId.length <= 0) || (AddNewDeviceData.DeviceId.length > 100) || (AddNewDeviceData.password.length < 8)"
              color="var(--color-theme-text)"
              text="เพิ่ม"
              variant="tonal"
              @click="isActiveAddExistPage = false; genToken(); dataStoreAddNew(); this.mqttclient.publish(AddNewDeviceData.token + '/Device', 'HEARTBEAT');"
            ></v-btn>
            
          </v-card-actions>
        </v-card>
      </template>
    </v-dialog>

    <div class="header">
      <TextLogo class="Splash-Text" ref="SplashText"/>
      <button class="addbtn" id="addbtn-menuactivator" >
        <svg width="14" height="11" viewBox="0 0 14 11" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path d="M6.3815 11C5.9335 11 5.7095 10.776 5.7095 10.328V6.68H1.6135C1.1655 6.68 0.9415 6.456 0.9415 6.008V5.016C0.9415 4.568 1.1655 4.344 1.6135 4.344H5.7095V0.664C5.7095 0.216 5.9335 -0.00800037 6.3815 -0.00800037H7.5975C8.0455 -0.00800037 8.2695 0.216 8.2695 0.664V4.344H12.3975C12.8455 4.344 13.0695 4.568 13.0695 5.016V6.008C13.0695 6.456 12.8455 6.68 12.3975 6.68H8.2695V10.328C8.2695 10.776 8.0455 11 7.5975 11H6.3815Z" fill="black"/>
        </svg>
      </button>

      <v-menu activator="#addbtn-menuactivator" location="start" offset="5px">
        <v-list>
          <v-list-item
            v-for="(item, index) in items"
            :key="index"
            :value="index"
          >
            <v-list-item-title @click="item.func">{{ item.title }}</v-list-item-title>
          </v-list-item>
        </v-list>
      </v-menu>

    </div>

    <div class="list-device">

      <div class="devicecard" v-for="(Device, index) in Devices">
        <span>
          <span>
            <div class="device-name">{{ Device.name }}</div>
            <div class="device-id">{{ Device.id }}</div>
          </span>
          <div class="device-connect">{{ Device.connected ? 'เชื่อมต่อแล้ว' : 'ไม่มีสัญญาณ' }}</div>
        </span>

        <v-btn class="device-open" :loading="Device.loading" style="width: 80px; height: 80px; border-radius: 100%;" base-color="var(--color-theme)" @click="Device.loading = true; TimeOn(Device.token)">
          <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" width="80%">
            <path d="M0 192c0-35.3 28.7-64 64-64c.5 0 1.1 0 1.6 0C73 91.5 105.3 64 144 64c15 0 29 4.1 40.9 11.2C198.2 49.6 225.1 32 256 32s57.8 17.6 71.1 43.2C339 68.1 353 64 368 64c38.7 0 71 27.5 78.4 64c.5 0 1.1 0 1.6 0c35.3 0 64 28.7 64 64c0 11.7-3.1 22.6-8.6 32L8.6 224C3.1 214.6 0 203.7 0 192zm0 91.4C0 268.3 12.3 256 27.4 256l457.1 0c15.1 0 27.4 12.3 27.4 27.4c0 70.5-44.4 130.7-106.7 154.1L403.5 452c-2 16-15.6 28-31.8 28l-231.5 0c-16.1 0-29.8-12-31.8-28l-1.8-14.4C44.4 414.1 0 353.9 0 283.4z"/>
          </svg>
        </v-btn>

      </div>

    </div>

  </div>
  
  
</template>

<script scoped>


export default {
    data() {
        return {
          db: null,
          mqttclient: null,
          isActiveAddNewPage: false,
          isActiveAddNewPageNext: false,
          isActiveAddExistPage: false,
          items: [
            { title: 'เพิ่มอุปกรณ์ใหม่', func: this.turnOnAddNewPage },
            { title: 'เพิ่มอุปกรณ์ที่มีอยู่', func: this.turnOnAddExistPage},
          ],

          DeviceControl: {
            isActiveDeviceControl: false
          },

					Devices: [
						
					],

          AddNewDeviceData: {
            DeviceName: '',
            DeviceId: '',
            password: '',

            token: '',

            idform: {
              rules: {
                required: value => !!value || 'จำเป็น',
                max: v => v.length <= 100 || 'ห้ามเกิน 100 ตัวอักษร',
              }
            },

            pwform: {
              show: false,
              rules: {
                required: value => !!value || 'จำเป็น',
                min: v => v.length >= 8 || 'ขั้นต่ำ 8 ตัวอักษร',
              }
            }
          }
        }
    },
    methods: {
        turnOnAddNewPage() {
          this.isActiveAddNewPage = !this.isActiveAddNewPage
          this.isActiveAddNewPageNext = false
          this.AddNewDeviceData.DeviceName = ''
          this.AddNewDeviceData.token = ''
          this.AddNewDeviceData.DeviceId = ''
          this.AddNewDeviceData.password = ''
        },
        turnOnAddExistPage() {
          this.isActiveAddExistPage = !this.isActiveAddExistPage
          this.AddNewDeviceData.DeviceName = ''
          this.AddNewDeviceData.token = ''
          this.AddNewDeviceData.DeviceId = ''
          this.AddNewDeviceData.password = ''
        },

        genToken() {
          this.AddNewDeviceData.token = btoa(this.AddNewDeviceData.DeviceId + "welovefeedpal" + this.AddNewDeviceData.password)
          console.log(btoa(this.AddNewDeviceData.DeviceId + "welovefeedpal" + this.AddNewDeviceData.password))
        },

        openDB() {
          return new Promise((resolve, reject) => {
                const request = indexedDB.open("FeedPal", 1);

                request.onerror = (event) => {
                    console.error('Database error:', event.target.errorCode);
                    reject(event.target.errorCode);
                };

                request.onsuccess = (event) => {
                    this.db = request.result;
                    console.log(this.db);
                    resolve(this.db);
                };

                request.onupgradeneeded = (event) => {
                    this.db = event.target.result;
                    console.log(this.db);

                    // Create an objectStore for this database
                    const objectStore = this.db.createObjectStore("devices", { keyPath: "id" });
                    objectStore.createIndex("id", "id", { unique: true });
                };
            });
        },

        async dataStoreAddNew() {
          if (!this.db) {
            console.log("HEY")
            await this.openDB();
          }

          const transaction = this.db.transaction(["devices"], "readwrite");
          const objectStore = transaction.objectStore("devices");

          const thisdevice = {
            name: this.AddNewDeviceData.DeviceName,
            id: this.AddNewDeviceData.DeviceId,
            token: this.AddNewDeviceData.token,
          }

          objectStore.add(thisdevice);

          transaction.oncomplete = () => {
              console.log('Device added to the store:', thisdevice);
          };
          transaction.onerror = (event) => {
              console.error('Transaction error:', event.target.errorCode);
          };

          this.getAllDevices()
        },

        async getAllDevices(){
            if (!this.db) {
              console.log("HEY")
              await this.openDB();
            }
            const request = this.db.transaction('devices')
                              .objectStore('devices')
                              .openCursor();

		        this.Devices = []
            request.onsuccess = ()=> {
                const cursor = request.result;
            
                if (cursor) {
                    console.log(cursor.value.token);
										this.Devices.push(cursor.value)

                    this.mqttclient.subscribe(cursor.value.token + "/Client");
                    this.mqttclient.publish(cursor.value.token + "/Device", "HEARTBEAT");

                    cursor.continue();
                } else {
                    console.log('No more entries')
                }
              
            }
        },

        TimeOn(token) {
          this.mqttclient.publish(token + "/Device", "Time_On");
        }
    },
    mounted() {
      this.openDB()

      const protocol = 'ws'
      const host = 'rasp.etamarov.me'
      const port = '9001'
      const path = '/mqtt'
      const clientId = `mqtt_${Math.random().toString(16).slice(3)}`

      const connectUrl = `${protocol}://${host}:${port}${path}`

      this.mqttclient = mqtt.connect(connectUrl, {
        clientId,
        clean: true,
        connectTimeout: 4000,
        username: 'feedpal',
        password: 'FeedPal1234567',
        reconnectPeriod: 1000,
      })

      this.mqttclient.on('connect', () => {
        this.$store.state.server_connected = true
        console.log('Connected')
      })

      this.mqttclient.on("message", (topic, message) => {
        // message is Buffer
        console.log(topic + " : " + message.toString());

        if (message == "HEARTBEAT") {
          const findindex = this.Devices.findIndex(({ token }) => token === topic.replace('/Client',''))
          this.Devices[findindex].connected = true
        }
        if (message == "Off") {
          const findindex = this.Devices.findIndex(({ token }) => token === topic.replace('/Client',''))
          this.Devices[findindex].loading = false
        }
      });

      this.getAllDevices()

      
    }
};

</script>

<style lang="scss" scoped>
  body {
    --corner-radius: 0px; /* Default value */
  }
  .main {
    padding-top: env(safe-area-inset-top);

    margin: 0 auto;
    display: flex;
    flex-direction: column;
    width: calc(100% - 3rem);
    max-width: 62.5rem;
  }
  .header {
    margin-top: 1.5rem;
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: center;

  }
  .Splash-Text {
    fill: var(--color-heading);
  }

  .addbtn {
    border-radius: 100%;
    height: 24px;
    width: 24px;
    background-color: var(--color-theme);
    
    display: flex;
    justify-content: center;
    text-align: center;
    align-items: center;
  }
  
  .list-device {
    margin-top: 20px;
    display: flex;
    flex-direction: column;
    row-gap: 10px;
  }

  .devicecard {
    background-color: var(--color-background-soft);
    border-radius: 14px;
    height: 125px;

    padding: 10px 15px;

    display: flex;
    flex-direction: row;
    justify-content: space-between;
    transition: all 100ms;
    align-items: center;
  }



  .devicecard span {
    height: 100%;
    display: flex;
    flex-direction: column;
    text-align: left;
  }

  .device-name {
    font-size: 22px;
    font-weight: 600;

    color: var(--color-heading);
  }

  .device-id {
    font-size: 14px;
  }

  .device-connect {
    position: relative;
    bottom: 0;

    font-weight: 400;

    font-size: 14px;
  }

</style>
