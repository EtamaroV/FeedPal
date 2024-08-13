<script setup>
import TextLogo from '@/components/icons/TextLogo.vue';

import mqtt from 'mqtt';

const protocol = 'wss'
const host = 'rasp.etamarov.me'
const port = '9001'
const path = '/mqtt'
const clientId = `mqtt_${Math.random().toString(16).slice(3)}`

const connectUrl = `${protocol}://${host}:${port}${path}`

const client = mqtt.connect(connectUrl, {
  clientId,
  clean: true,
  connectTimeout: 4000,
  username: 'feedpal',
  password: 'FeedPal1234567',
  reconnectPeriod: 1000,
})

client.on('connect', () => {
  console.log('Connected')
})

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

            <v-divider></v-divider>
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
              @click="isActiveAddNewPage = false"
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
              @click="isActiveAddNewPageNext = true; genToken(); dataStoreAddNew()"
            ></v-btn>

          </v-card-actions>
        </v-card>
      </template>
    </v-dialog>

    <v-dialog v-model="isActiveAddExistPage" max-width="500">
    
      <template v-slot:default>
        <v-card title="เพิ่มอุปกรณ์ที่มีอยู่">
          <v-card-text>
            Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.
          </v-card-text>
        
          <v-card-actions>
            <v-spacer></v-spacer>
          
            <v-btn
              text="Close Dialog"
              @click="isActiveAddExistPage = false; printStudents()"
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


  </div>
  
  
</template>

<script scoped>


export default {
    data() {
        return {
          db: null,
          isActiveAddNewPage: false,
          isActiveAddNewPageNext: false,
          isActiveAddExistPage: false,
          items: [
            { title: 'เพิ่มอุปกรณ์ใหม่', func: this.toggleAddNewPage },
            { title: 'เพิ่มอุปกรณ์ที่มีอยู่', func: this.toggleAddExistPage},
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
        toggleAddNewPage() {
          this.isActiveAddNewPage = !this.isActiveAddNewPage
          this.isActiveAddNewPageNext = false
          this.AddNewDeviceData.token = ''
          this.AddNewDeviceData.DeviceId = ''
          this.AddNewDeviceData.password = ''
        },
        toggleAddExistPage() {
          this.isActiveAddExistPage = !this.isActiveAddExistPage
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

        async  dataStoreAddNew() {
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
        },

        printStudents(){
            const request = this.db.transaction('devices')
                              .objectStore('devices')
                              .openCursor();

            request.onsuccess = ()=> {
                const cursor = request.result;
            
                if(cursor){
                    console.log(cursor.value);
                    cursor.continue();
                }else{
                    console.log('No more entries')
                }
              
            }
        }
    },
    mounted() {
      this.openDB()
      
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

</style>
