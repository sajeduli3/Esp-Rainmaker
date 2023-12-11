// This example demonstrates the ESP RainMaker with a standard Switch device.
#include "RMaker.h"
#include "WiFi.h"
#include "WiFiProv.h"
#include "AppInsights.h"
#include <Preferences.h>
Preferences pref;

#define DEFAULT_POWER_MODE true
const char *service_name = "ESPC3";
const char *pop = "sp555666";
char device1[] = "Switch1";
char device2[] = "Switch2";

static uint8_t RELAY_1 = 7;
static uint8_t RELAY_2 = 6;

static uint8_t SwitchPin1 = 0;  //D13
static uint8_t SwitchPin2 = 1;


// GPIO for push button
//#if CONFIG_IDF_TARGET_ESP32C3
//static int gpio_0 = 9;
//static int RELAY_1 = 7; //

//#else
// GPIO for virtual device
static int gpio_0 = 9;
static uint8_t WIFI_LED = 8;
//static int RELAY_1 = 16;
//#endif

/* Variable for reading pin status*/
bool STATE_RELAY_1 = LOW;
bool STATE_RELAY_2 = LOW;

bool SwitchState_1 = LOW;
bool SwitchState_2 = LOW;


// The framework provides some standard device types like switch, lightbulb,
// fan, temperaturesensor.
static Switch my_switch1(device1, &RELAY_1);
static Switch my_switch2(device2, &RELAY_2);
void sysProvEvent(arduino_event_t *sys_event) {
  switch (sys_event->event_id) {
    case ARDUINO_EVENT_PROV_START:
#if CONFIG_IDF_TARGET_ESP32S2
      Serial.printf("\nProvisioning Started with name \"%s\" and PoP \"%s\" on SoftAP\n",
                    service_name, pop);
      printQR(service_name, pop, "softap");
#else
      Serial.printf("\nProvisioning Started with name \"%s\" and PoP \"%s\" on BLE\n",
                    service_name, pop);
      printQR(service_name, pop, "ble");
#endif
      break;
    case ARDUINO_EVENT_PROV_INIT:
      wifi_prov_mgr_disable_auto_stop(10000);
      break;
    case ARDUINO_EVENT_PROV_CRED_SUCCESS:
      wifi_prov_mgr_stop_provisioning();
      break;
    default:;
  }
}

void write_callback(Device *device, Param *param, const param_val_t val,
                    void *priv_data, write_ctx_t *ctx) {
  const char *device_name = device->getDeviceName();
  const char *param_name = param->getParamName();

  if (strcmp(device_name, device1) == 0) {

    Serial.printf("Lightbulb1 = %s\n", val.val.b ? "true" : "false");

    if (strcmp(param_name, "Power") == 0) {
      Serial.printf("Received value = %s for %s - %s\n", val.val.b? "true" : "false", device_name, param_name);
      STATE_RELAY_1 = val.val.b;
      //STATE_RELAY_1 = !STATE_RELAY_1;
      
      (STATE_RELAY_1 == false) ? digitalWrite(RELAY_1, HIGH) : digitalWrite(RELAY_1, LOW);
      param->updateAndReport(val);
      pref.putBool("Relay1" , STATE_RELAY_1);
    }
  } else if (strcmp(device_name, device2) == 0) {

    Serial.printf("Switch value = %s\n", val.val.b ? "true" : "false");

    if (strcmp(param_name, "Power") == 0) {
      Serial.printf("Received value = %s for %s - %s\n", val.val.b? "true" : "false", device_name, param_name);
      STATE_RELAY_2 = val.val.b;
      //STATE_RELAY_2 = !STATE_RELAY_2;
      
      (STATE_RELAY_2 == false) ? digitalWrite(RELAY_2, HIGH) : digitalWrite(RELAY_2, LOW);
      param->updateAndReport(val);
      pref.putBool("Relay2" , STATE_RELAY_2);
    }
  }
}

void manual_control()
{
  if (digitalRead(SwitchPin1) == LOW && SwitchState_1 == LOW) {
    digitalWrite(RELAY_1, LOW);
    STATE_RELAY_1 = 1;
    SwitchState_1 = HIGH;
    pref.putBool("Relay1", STATE_RELAY_1);
    my_switch1.updateAndReportParam(ESP_RMAKER_DEF_POWER_NAME, STATE_RELAY_1);
    Serial.println("Switch-1 on");
  }
  if (digitalRead(SwitchPin1) == HIGH && SwitchState_1 == HIGH) {
    digitalWrite(RELAY_1, HIGH);
    STATE_RELAY_1 = 0;
    SwitchState_1 = LOW;
    pref.putBool("Relay1", STATE_RELAY_1);
    my_switch1.updateAndReportParam(ESP_RMAKER_DEF_POWER_NAME, STATE_RELAY_1);
    Serial.println("Switch-1 off");
  }
  if (digitalRead(SwitchPin2) == LOW && SwitchState_2 == LOW) {
    digitalWrite(RELAY_2, LOW);
    STATE_RELAY_2 = 1;
    SwitchState_2 = HIGH;
    pref.putBool("Relay2", STATE_RELAY_2);
    my_switch2.updateAndReportParam(ESP_RMAKER_DEF_POWER_NAME, STATE_RELAY_2);
    Serial.println("Switch-2 on");
  }
  if (digitalRead(SwitchPin2) == HIGH && SwitchState_2 == HIGH) {
    digitalWrite(RELAY_2, HIGH);
    STATE_RELAY_2 = 0;
    SwitchState_2 = LOW;
    pref.putBool("Relay2", STATE_RELAY_2);
    my_switch2.updateAndReportParam(ESP_RMAKER_DEF_POWER_NAME, STATE_RELAY_2);
    Serial.println("Switch-2 off");
  }
}

void getRelayState()
{
  STATE_RELAY_1 = pref.getBool("Relay1", 0);
  digitalWrite(RELAY_1, !STATE_RELAY_1); 
  my_switch1.updateAndReportParam(ESP_RMAKER_DEF_POWER_NAME, STATE_RELAY_1);
  delay(200);
  STATE_RELAY_2 = pref.getBool("Relay2", 0);
  digitalWrite(RELAY_2, !STATE_RELAY_2); 
  my_switch2.updateAndReportParam(ESP_RMAKER_DEF_POWER_NAME, STATE_RELAY_2);
  delay(200);
  }

void setup() 
{
  
  Serial.begin(115200);
  pref.begin("Relay_State", false);
  pinMode(gpio_0, INPUT);
  pinMode(RELAY_1, OUTPUT);
  pinMode(RELAY_2, OUTPUT);
 pinMode(SwitchPin1, INPUT_PULLUP);
    pinMode(SwitchPin2, INPUT_PULLUP);  
  pinMode(WIFI_LED, OUTPUT);
  digitalWrite(WIFI_LED, LOW);

  digitalWrite(RELAY_1, !STATE_RELAY_1);
  digitalWrite(RELAY_2, !STATE_RELAY_2);

  Node my_node;
  my_node = RMaker.initNode("ESP Bristy Node");

  // // Initialize switch device
  // my_switch = new Switch("Switch", &RELAY_1);
  // if (!my_switch) {
  //   return;
  // }
  // Standard switch device
   my_switch1.addCb(write_callback);
  my_switch2.addCb(write_callback);

  // Add switch device to the node
   my_node.addDevice(my_switch1);
  my_node.addDevice(my_switch2);

  // This is optional
  RMaker.enableOTA(OTA_USING_TOPICS);
  // If you want to enable scheduling, set time zone for your region using
  // setTimeZone(). The list of available values are provided here
  // https://rainmaker.espressif.com/docs/time-service.html
  //  RMaker.setTimeZone("Asia/Shanghai");
  //  Alternatively, enable the Timezone service and let the phone apps set the
  //  appropriate timezone
  RMaker.enableTZService();

  RMaker.enableSchedule();

  RMaker.enableScenes();
  // Enable ESP Insights. Insteads of using the default http transport, this function will
  // reuse the existing MQTT connection of Rainmaker, thereby saving memory space.
  initAppInsights();

  RMaker.enableSystemService(SYSTEM_SERV_FLAGS_ALL, 2, 2, 2);

  RMaker.start();

  WiFi.onEvent(sysProvEvent);
#if CONFIG_IDF_TARGET_ESP32S2
  WiFiProv.beginProvision(WIFI_PROV_SCHEME_SOFTAP, WIFI_PROV_SCHEME_HANDLER_NONE,
                          WIFI_PROV_SECURITY_1, pop, service_name);
#else
  WiFiProv.beginProvision(WIFI_PROV_SCHEME_BLE, WIFI_PROV_SCHEME_HANDLER_FREE_BTDM,
                          WIFI_PROV_SECURITY_1, pop, service_name);
#endif

 getRelayState();

}
void loop() {
  if (digitalRead(gpio_0) == LOW) {  // Push button pressed

    // Key debounce handling
    delay(100);
    int startTime = millis();
    while (digitalRead(gpio_0) == LOW) {
      delay(50);
    }
    int endTime = millis();

    if ((endTime - startTime) > 10000) {
      // If key pressed for more than 10secs, reset all
      Serial.printf("Reset to factory.\n");
      RMakerFactoryReset(2);
    } else if ((endTime - startTime) > 3000) {
      Serial.printf("Reset Wi-Fi.\n");
      // If key pressed for more than 3secs, but less than 10, reset Wi-Fi
      RMakerWiFiReset(2);
    }
  }
    delay(100);
  
  if (WiFi.status() != WL_CONNECTED){
    Serial.println("WiFi Not Connected");
    digitalWrite(WIFI_LED, LOW);
  }
  else{
   Serial.println("WiFi Connected");
    digitalWrite(WIFI_LED, HIGH);
  }
  manual_control();
}

