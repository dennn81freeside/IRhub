/*
 * http://ruben1.narod.ru/hobby/arduino/esp8266_ir.html
 * Ruben Lachinov 27.07.2016
 *  Подключен ретранслятор!
 *
  find ****** найдите и замените

  SD card  >> esp8266 12
  3v3
  cs   - io2
  mosi - io13
  clk  - io14
  miso - io12
  gnd
*/

#include <Time.h>
#include <TimeLib.h>
#include <DS1302RTC.h>
//==WiFi==

#include <ESP8266WiFi.h>
#include <ESP8266mDNS.h>
#include <pgmspace.h>   //PROGMEM

// == SD==
#include <SD.h>
#include <SPI.h>
// set up variables using the SD utility library functions:
Sd2Card card;
SdVolume volume;
SdFile root;
const int chipSelect = 15;
File myFile;
String File_name;
String File_patch;
//==IR==
#include <IRremoteESP8266.h>
int RECV_PIN = 2;                 //IR detector ( GPIO pin 2 = D4)
//int RECV_PIN = 0;                 //IR detector ( GPIO pin 0 = D3)
IRrecv irrecv(RECV_PIN);
IRsend irsend(0);                 //an IR led is connected to GPIO pin 5 = D1, pin0 = D3
decode_results results;

//==html==
const static char header0[] PROGMEM =
  "<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Transitional//EN'>\n\r"
  "<meta http-equiv='Content-Type' content='text/html; charset=UTF-8' />\n\r"
  "<html><head><title>ESP8266 GPIO Control</title>\n\r"
  "<meta name=viewport content=\"width=device-width, initial-scale=1\">\n\r"
  "<style type=\"text/css\">\n\r"
  "#nav {border:2px solid #000047;border-radius:3px;-moz-border-radius:3px;-webkit-border-radius:3px;}\n\r"
  "#nav, #nav ul {list-style:none;padding:0;width:166px;}\n\r"
  "#nav ul {position:relative;z-index:-1;}\n\r"
  "#nav li {position:relative;z-index:100;}\n\r"
  "#nav ul li {margin-top:-23px;-moz-transition:  0.4s linear 0.4s;-ms-transition: 0.4s linear 0.4s;-o-transition: 0.4s linear 0.4s;\n\r"
  "-webkit-transition: 0.4s linear 0.4s;transition: 0.4s linear 0.4s;}\n\r"
  "#nav li a {background-color:#d4d5d8;color:#000;display:block;font-size:12px;font-weight:bold;line-height:28px;outline:0;padding-left:15px;text-decoration:none;}\n\r"
  "#nav li a.sub {color:#fff; padding-left:-15px;text-align:center;background:#0000ff;}\n\r"
  "#nav li a:hover {background-color:#bcbdc1;}\n\r"
  "#nav ul li a {background-color:#00e;border-bottom:1px solid #ccc;color:#fff;font-size:16px;line-height:22px;}\n\r"
  "#nav ul li a:hover {background-color:#ddd;color:#444;}\n\r"
  "#nav a.sub:focus {background:#bcbdc1;outline:0;}\n\r"
  "#nav a:focus ~ ul li { margin-top:0; -moz-transition:  0.4s linear; -ms-transition: 0.4s linear; -o-transition: 0.4s linears;\n\r"
  " -webkit-transition: 0.4s linears; transition: 0.4s linear;}\n\r"
  "#nav a.sub:active {background:#bcbdc1;outline:0;}\n\r"
  "#nav a:active ~ ul li { margin-top:0;}\n\r"
  "#nav ul:hover {display:block;}\n\r";

const static char header1[] PROGMEM =
  "a {color:#FFF;font-weight:bold;text-decoration:none;}\n\r"
  ".cleared {float : none;clear : both;margin : 0;padding : 0;border : none;font-size : 1px;}\n\r"
  ".key {display: block;cursor: pointer;color: #444;font: bold 9pt arial;text-decoration: none;text-align: center;\n\r"
  "width: 44px;height: 41px;margin: 5px;background: #DDD1CC;\n\r"
  "-webkit-box-shadow: 0 0 8px rgba(0, 0, 0, .75);-moz-box-shadow: 0 0 8px rgba(0, 0, 0, .75);box-shadow: 1px 1px 8px rgba(0, 0, 0, .75);\n\r"
  "-moz-border-radius: 4px;border-radius: 4px;border-top: 1px solid #b5b5b5;\n\r"
  "-webkit-box-shadow: inset 0 0 25px #e8e8e8, 0 1px 0 #c3c3c3, 0 2px 0 #c9c9c9, 0 2px 3px #333;\n\r"
  "-moz-box-shadow: inset 0 0 25px #e8e8e8, 0 1px 0 #c3c3c3, 0 2px 0 #c9c9c9, 0 2px 3px #333;\n\r"
  "box-shadow: inset 0 0 25px #e8e8e8, 0 1px 0 #c3c3c3, 0 2px 0 #c9c9c9, 0 2px 3px #333;\n\r"
  "text-shadow: 0px 1px 0px #f5f5f5;}\n\r"
  ".key:active, .keydown {color: #00F;background: #D9D9FF;margin: 7px 5px 3px;\n\r"
  "-webkit-box-shadow: inset 0 0 25px #ddd, 0 0 3px #333;\n\r"
  "-moz-box-shadow: inset 0 0 25px #ddd, 0 0 3px #333;\n\r"
  "box-shadow: inset 0 0 25px #ddd, 0 0 3px #333;\n\r"
  "border-top: 1px solid #eee;}\n\r"
  ".key span {display: block;margin: 13px 0 0;text-transform: uppercase;}\n\r";

const static char header2[] PROGMEM =
  ".but {display: block;cursor: pointer;color: #333;font: bold 9pt arial;text-decoration: none;text-align: center;\n\r"
  "width: 44px;height: 41px;margin: 5px;background: #DDD1CC;\n\r"
  "-webkit-border-radius: 22px;-moz-border-radius: 22px;border-radius: 22px;\n\r"
  "-webkit-box-shadow: 0 0 8px rgba(0, 0, 0, .75);\n\r"
  "-moz-box-shadow: 0 0 8px rgba(0, 0, 0, .75);\n\r"
  "box-shadow: 1px 1px 8px rgba(0, 0, 0, .75);\n\r"
  "border-top: 1px solid #b5b5b5;\n\r"
  "-webkit-box-shadow: inset 0 0 25px #e8e8e8, 0 1px 0 #c3c3c3, 0 2px 0 #c9c9c9, 0 2px 3px #333;\n\r"
  "-moz-box-shadow: inset 0 0 25px #e8e8e8, 0 1px 0 #c3c3c3, 0 2px 0 #c9c9c9, 0 2px 3px #333;\n\r"
  "box-shadow: inset 0 0 25px #e8e8e8, 0 1px 0 #c3c3c3, 0 2px 0 #c9c9c9, 0 2px 3px #333;\n\r"
  "text-shadow: 0px 1px 0px #f5f5f5;}\n\r"
  ".but:active, .keydown {color: #00F;background: #D9D9FF;margin: 7px 5px 3px;\n\r"
  "-webkit-box-shadow: inset 0 0 25px #ddd, 0 0 3px #333;\n\r"
  "-moz-box-shadow: inset 0 0 25px #ddd, 0 0 3px #333;\n\r"
  "box-shadow: inset 0 0 25px #ddd, 0 0 3px #333;\n\r"
  "border-top: 1px solid #eee;}\n\r"
  ".but span {display: block;margin: 13px 0 0;text-transform: uppercase;}\n\r";

const static char header3[] PROGMEM =
  ".onoffswitch {position: relative;width: 24px;-webkit-user-select:none;-moz-user-select:none;-ms-user-select: none;}\n\r"
  ".onoffswitch-checkbox {display: none;}\n\r"
  ".onoffswitch-label {display: block;overflow: hidden;cursor: pointer;border: 2px solid #8084B0;border-radius: 10px;}\n\r"
  ".onoffswitch-inner {display: block;width: 200%;margin-left: 0%;transition: margin 0.3s ease-in 0s;}\n\r"
  ".onoffswitch-inner:before, .onoffswitch-inner:after {display: block;float: left;width: 50%;height: 6px;padding: 0;line-height: 6px;font-size: 14px;color: white;font-family: Trebuchet, Arial, sans-serif;font-weight: bold;box-sizing: border-box;}\n\r"
  ".onoffswitch-inner:before {content: \"\";padding-left: 10px;background-color: #FF0000;color: #999999;}\n\r"
  ".onoffswitch-inner:after {content: \"\";padding-right: 10px;background-color: #0AFF0A;color: #FFFFFF;text-align: right;}\n\r"
  ".onoffswitch-switch {display: block;width: 10px;margin: -2px;background: #FFFFFF;position: absolute;top: 0;bottom: 0;right: 14px;border: 2px solid #8084B0;border-radius: 10px;transition: all 0.3s ease-in 0s;}\n\r"
  ".onoffswitch-switch2 {display: block;width: 10px;margin: -2px;background: #FFFFFF;position: absolute;top: 0;bottom: 0;right: 0px;border: 2px solid #8084B0;border-radius: 10px;transition: all 0.3s ease-in 0s;}\n\r"
  ".onoffswitch-checkbox:checked + .onoffswitch-label .onoffswitch-inner {margin-left: -100%;}\n\r"
  ".onoffswitch-checkbox:checked + .onoffswitch-label .onoffswitch-switch {right: 0px;}\n\r"
  ".onoffswitch-checkbox:checked + .onoffswitch-label .onoffswitch-switch2 {right: 14px;}\n\r";

const static char header4[] PROGMEM =
  ".onoffswitch-inner2 {display: block;width: 200%;margin-left: -100%;transition: margin 0.3s ease-in 0s;}\n\r"
  ".onoffswitch-inner2:before, .onoffswitch-inner2:after {display: block;float: left;width: 50%;height: 6px;padding: 0;line-height: 6px;font-size: 14px;color: white;font-family: Trebuchet, Arial, sans-serif;font-weight: bold;box-sizing: border-box;}\n\r"
  ".onoffswitch-inner2:before {content: \"\";padding-right: 10px;background-color: #0AFF0A;color: #FFFFFF;text-align: right;}\n\r"
  ".onoffswitch-inner2:after {content: \"\";padding-left: 10px;background-color: #FF0000;color: #999999;}\n\r"
  ".onoffswitch-checkbox:checked + .onoffswitch-label .onoffswitch-inner2 {margin-left: 0;}\n\r"
  ".out_val {-webkit-appearance: none;appearance: none;padding: 0;margin-left:40px;border: 2px solid #eee;border-radius: 2px;box-shadow: inset 0 1px #ccc, inset 0 1px #575555, inset 0 -1px #00c;background: #fef linear-gradient(#BCBCBC, #fff0f5);overflow: hidden;}\n\r"
  "div.ex_output{width:258px;text-align:center;background-color: #000080;}\n\r"
  "div.ex_output output{font-weight:bold;font-size:16px;color: #CCC;}\n\r"
  "div.ex_pult {width:162px;text-align:center;background-color: #000080;margin-left:6px;}\n\r"
  "div.ex_pult output {font-weight:bold;font-size:16px;color: #CCC;}\n\r"
  "div.output {width:80px;background-color: #EFE3CF;border: 2px solid #006;text-align:center;margin-bottom:4px;margin-right:2px;float:left;}\n\r"
  "div.output div {width:80px;background-color:#C1C5F7;}\n\r"
  "div.output output {font-weight:bold;font-size:16px;color:#009;}\n\r";

const static char header5[] PROGMEM =
  ".rangeP {-webkit-appearance: none;appearance: none;padding: 0;border: 2px solid #eee;border-radius: 2px;background: #fef linear-gradient(#BCBCBC, #fff0f5);overflow: hidden;}\n\r"
  ".rangeP::-moz-range-thumb {border-radius: 2px;box-shadow: -13px 0 #40310a, -26px 0 #40310a, -39px 0 #40310a, -52px 0 #40310a, -65px 0 #40310a, -78px 0 #40310a, -91px 0 #40310a, -104px 0 #40310a, -117px 0 #40310a, -130px 0 #40310a, -143px 0 #40310a, -156px 0 #40310a;}\n\r"
  ".rangeP::-moz-range-track {background: none;border: none;}\n\r"
  ".rangeP::-webkit-slider-thumb { -webkit-appearance: none; width:15px; height:8px; border: 1px solid #000081; border-radius: 2px; background-image:linear-gradient(#dedede, #8282ff); box-shadow: -13px 0 #1c59f4, -26px 0 #1c59f4, -39px 0 #1c55eb, -52px 0 #1c50db, -65px 0 #1b49c9, -78px 0 #1b43b7, -91px 0 #1b3a9e, -104px 0 #1b338d, -117px 0 #1b2d7a, -130px 0 #1b286e, -143px 0 #1b286d, -156px 0 #1b2566;}\n\r"
  ".column_view-off {display: none;text-align: center;color: #000;}\n\r"
  ".column_view-on {background-color: #cbBEFF;}.mode_view-off {display: none;}.mode_view-on {background-color: #D1D2F3;}\n\r"
  ".css-checkbox input[type=checkbox] {\n\r"
  "display: none;}\n\r"
  ".css-checkbox label {\n\r"
  "position: relative;display: block;width: 64px;height: 10px;padding: 15px;\n\r"
  "border-radius: 50px;line-height: 10px;color: #31b3ff;font-size:48px;\n\r"
  "color: #0F0;text-shadow: 1px 1px 0px rgba(255, 255, 255, .15);background: rgb(71, 71, 71);\n\r"
  "box-shadow:\n\r"
  " 0 1px 0 rgba(255, 255, 255, .2), inset 0 0 0 5px rgb(60, 60, 60), inset 0 6px 6px rgba(0, 0, 0, .5), inset 0 -6px 1px rgba(255, 255, 255, .2);\n\r"
  "cursor: pointer;}\n\r"
  ".css-checkbox label:before {\n\r"
  "content: \"•\";font-size:48px;position: absolute;right: 15px;color: #F00;}\n\r"
  ".css-checkbox label:after {\n\r"
  "content: \"\";position: absolute;left: 5px;top: 5px;display: block;\n\r"
  "width: 50px;height: 30px;border-radius: 50px;\n\r"
  "background: #ccc linear-gradient(#fcfff4 0%, #dfe5d7 40%, #b3bead 100%);\n\r"
  "transition: .5s;}\n\r"
  ".css-checkbox input[type=checkbox]:checked + label:after {\n\r"
  "left: 40px;}\n\r"
  "</style>\n\r";

const static char header6[] PROGMEM =
  "<script type= \"text/javascript\">\n\r"
  "var get_ver = 1;\n\r"
  "var timer2;\n\r"
  "var str_URL = \"\"; var x = \"\";\n\r"
  "function sel_but(id_cb) {\n\r"
  "str_URL = id_cb.id;\n\r"
  "trans_par(id_cb);}\n\r"

  "function trans_par(id_cb) {\n\r"
  "var go_link = \"?\";\n\r"
  "if (document.getElementById(\"ip\").title != \"#\")  //есть WiFi IP\n\r"
  "{//var mess_zz=\"zz=1\"; //это будет запрос для внешнего модуля\n\r"
  "switch (get_ver) {\n\r"
  "case 1: go_link = go_link + \"ip=\" + document.getElementById(\"ip\").title + ';'; break\n\r"
  "case 2: go_link = \"http://\" + document.getElementById(\"ip\").title + \"?ip=192.168.0.128\" + ';'; break\n\r"
  "default: alert(\"no get\"); break}}else{ mess_zz=\"zz=0\";}\n\r"
  "//var lag = document.getElementById(\"r_sec2\").value;\n\r"
  "if (document.getElementById(\"te2\")){\n\r"
  "if (document.getElementById(\"te2\").checked) {\n\r"
  "timer2 = window.setTimeout(function(){str_URL2 =  \"?\" + document.getElementById(\"pult\").title  + \":\" + \"zz=0\";trans_get();},2000);}}\n\r"
  "if (document.getElementById('pultname')) { //pultname только для IR\n\r"
  "str_URL2 = go_link + document.getElementById(\"pultname\").textContent  + ':' + str_URL;\n\r"
  "} else {str_URL2 = go_link + document.getElementById(\"pult\").title  + ':' + str_URL;}\n\r"
  "trans_get();}\n\r";


const static char header7[] PROGMEM =
  "function trans_get() {\n\r"
  "var xmlhttp = getXmlHttp(); \n\r"
  "//alert(\"str_URL2= \" + str_URL2);\n\r"
  "xmlhttp.open(\"GET\", str_URL2, true);\n\r"
  "var http = new XMLHttpRequest(), href = this.href;\n\r"
  "http.open(\"GET\", str_URL2);\n\r"
  "http.setRequestHeader(\"Content-Type\", \"application/x-www-form-urlencoded\");\n\r"
  "xmlhttp.onreadystatechange = function()\n\r"
  "{ if (xmlhttp.readyState == 4) {\n\r"
  "if (xmlhttp.status == 200) {\n\r"
  "x = xmlhttp.responseText;\n\r"
  "getParam(x);}}}\n\r"
  "xmlhttp.send(null);}\n\r";

const static char header8[] PROGMEM =
  "function getXmlHttp() {\n\r"
  "try {return new ActiveXObject(\"Msxml2.XMLHTTP\");}\n\r"
  "catch (e) {try {\n\r"
  "return new ActiveXObject(\"Microsoft.XMLHTTP\");}\n\r"
  "catch (ee) {}} if (typeof XMLHttpRequest != \"undefined\")\n\r"
  "{return new XMLHttpRequest();}}\n\r"
  "function getParam(get) {\n\r"
  "var tmp = new Array(); var tmp2 = new Array(); var param = new Array();\n\r"
  "//alert(\"get= \"+get)\n\r"
  "if (get != \"\") {\n\r"
  "tmp = (get.substr(1)).split(\"&\");\n\r"
  "for (var i = 0; i < tmp.length; i++) {\n\r"
  "tmp2 = tmp[i].split(\"=\");\n\r"
  "param[tmp2[0]] = tmp2[1];}\n\r"
  "for (var key in param) {\n\r"
  "if (param[key] != \"\") {\n\r"
  "if (key.indexOf(\"cb\") >= 0 || key.indexOf(\"ir\") >= 0) {\n\r"
  "//if (key.indexOf(\"cb\") >= 0) {\n\r"
  "if (param[key] > 0) {\n\r"
  "document.getElementById(key).checked = true;\n\r"
  "} else {\n\r"
  "document.getElementById(key).checked = false;}\n\r"
  "} else {\n\r"
  "document.getElementById(key).value = param[key];\n\r"
  "}}}}}\n\r";

const static char header9[] PROGMEM =
  "function toggleView(source, tableId, tag) {\n\r"
  "var elems = document.getElementById(tableId).getElementsByTagName(tag);\n\r"
  "for (i = 0; i < elems.length; ++i) {\n\r"
  "toggleClasses(elems[i], \"column_view-off\", \"column_view-on\");}\n\r"
  "return false;}\n\r"
  "function toggleView_mode(source, tableId, tag) {\n\r"
  "var elems = document.getElementById(tableId).getElementsByTagName(tag);\n\r"
  "for (i = 0; i < elems.length; ++i) {\n\r"
  "toggleClasses(elems[i], \"mode_view-off\", \"mode_view-on\");}\n\r"
  "return false;}\n\r"
  "function toggleClasses(elem, className1, className2) {\n\r"
  "var clazz = elem.className.toString();\n\r"
  "if (clazz.indexOf(className1) >= 0) {\n\r"
  "elem.className = clazz.replace(className1, className2);\n\r"
  "} else if (clazz.indexOf(className2) >= 0) {\n\r"
  "elem.className = clazz.replace(className2, className1);}}\n\r";

const static char header10[] PROGMEM =
  "function my_timer(id_cb){\n\r"
  "if (document.getElementById(\"tt1\").checked){my_timer0(id_cb);}}\n\r"
  "function my_timer0(id_cb){\n\r"
  "var interval_id = setInterval(function() {\n\r"
  "if (document.getElementById(\"tt1\").checked == false){\n\r"
  "clearInterval(interval_id); }\n\r"
  "var my_clock = document.getElementById(\"my_clock\");\n\r"
  "var time =my_clock.innerHTML;\n\r"
  "var arr = time.split(\":\");\n\r"
  "var m = arr[0];\n\r"
  "var s = arr[1];\n\r"
  "if (s == 0) {\n\r"
  "s = document.getElementById(\"r_sec\").value;\n\r"
  "if (document.getElementById(\"ip\").title != \"#\")  //есть WiFi IP\n\r"
  "{str_URL = \"zz=1\"; //это будет запрос для внешнего модуля\n\r"
  "}else{str_URL = \"zz=0\";}\n\r"
  "trans_par(id_cb);\n\r"
  "}else s--; \n\r"
  "if (s < 10) s = \"0\" + s;\n\r"
  "if (document.getElementById(\"tt1\").checked) {\n\r"
  "document.getElementById(\"my_clock\").innerHTML = m + \":\" + s;}\n\r"
  "}, 1000);}\n\r"

  "function startTimer() {\n\r"
  "if (document.getElementById(\"pultname\")){\n\r"
  "if (document.getElementById(\"pultname\").textContent == \"wait\")\n\r"
  "{var my_host = unescape(document.location.pathname);\n\r"
  "var r = my_host.substring( my_host.lastIndexOf('/') + 1, my_host.length );\n\r"
  "str_URL = \"pn=\" + my_host.substring( my_host.lastIndexOf('/') + 1, my_host.length );\n\r"
  "trans_par(document.getElementById(\"pultname\"));}}\n\r"
  "setTimeout(startTimer, 1000);}\n\r";

const static char header11[] PROGMEM =
  "function sd_index(id_cb) {\n\r"
  "if (id_cb.id[0] == \"c\") {\n\r"
  "var l = id_cb.id.length;\n\r"
  "var sd = \"s\";\n\r"
  "for (var i = 1; i < id_cb.id.length; i++)\n\r"
  "sd = sd + id_cb.id[i];\n\r"
  "} else {\n\r"
  "sd = \"s\" + id_cb.id;}\n\r"
  "var indout = document.getElementById(sd);\n\r"
  "if (indout != null){\n\r"
  "if (document.getElementById(\"ce1\").checked) {\n\r"
  "indout.selectedIndex  = 1;\n\r"
  "indout.style.backgroundColor = \"#fff894\";}}}\n\r"
  "function sel_test(id_cb) {\n\r"
  "var colo = id_cb.options[id_cb.selectedIndex].index;\n\r"
  "switch (colo) {\n\r"
  "case 0: set_color(id_cb, \"#94fff8\"); break\n\r"
  "case 1: set_color(id_cb, \"#fff894\"); break\n\r"
  "case 2: set_color(id_cb, \"#f294ff\"); break\n\r"
  "default: set_color(id_cb, \"#FFFFFF\"); break}\n\r"
  "x = id_cb.id; str_URL = x + \"=\" + colo;\n\r"
  "trans_par(id_cb);}\n\r";

const static char header12[] PROGMEM =
  "function set_color(el, cl) {\n\r"
  "el.style.backgroundColor = cl;}\n\r"
  "function polsun_val(id_cb){\n\r"
  "var x = id_cb.id;\n\r"
  "var newstr = x.replace(new RegExp(\"cd\"),\"cb\");\n\r"
  "if(document.getElementById(newstr)){\n\r"
  "if (id_cb.value >0)\n\r"
  "{document.getElementById(newstr).checked=true;}else{document.getElementById(newstr).checked=false;}}\n\r"
  "str_URL = id_cb.id+\"=\" + id_cb.value;\n\r"
  "if (document.getElementById(\"pultname\").textContent == \"mp3\"){\n\r"
  "document.getElementById(\"result1\").value = id_cb.value;  \n\r"
  "}else{document.getElementById(\"result1\").value = str_URL;}\n\r"
  "sd_index(id_cb);trans_par(id_cb);}\n\r"

  "function cb_check0(id_cb) {\n\r"
  "y = id_cb.name;  //IP adress\n\r"
  "x = id_cb.id;\n\r"
  "if (x.indexOf(\"vv\") >=0 ){x=\"vv12\";}\n\r"
  "if (id_cb.checked) {\n\r"
  "str_URL = x + \"=1\";\n\r"
  "} else {str_URL = x + \"=0\";}\n\r"
  "if(y !=\"\"){\n\r"
  "var temp_ip = document.getElementById(\"ip\").title;\n\r"
  "document.getElementById(\"ip\").title = y;\n\r"
  "trans_par(id_cb);\n\r"
  "//восстановить значение\n\r"
  "document.getElementById(\"ip\").title = temp_ip;\n\r"
  "}else{\n\r"
  "trans_par(id_cb);}}\n\r"

  "function cb_check(id_cb) {\n\r"
  "x = id_cb.id;\n\r"
  "y = x.replace(\"cb\", \"cd\");\n\r"
  "var rng = document.getElementById(y);\n\r"
  "if (id_cb.checked) {\n\r"
  "str_URL = x + \"=1\";\n\r"
  "rng.value = rng.max; sd_index(id_cb);} else {\n\r"
  "str_URL = x + \"=0\";\n\r"
  "rng.value = rng.min;}\n\r"
  "trans_par(id_cb);}\n\r";

const static char header13[] PROGMEM =
  "function f_rec(id_cb){\n\r"
  "var yyy = document.getElementById(\"rec\").style.display;\n\r"
  "if (yyy == \"none\"){\n\r"
  "document.getElementById(\"rec\").style.display = \"block\";\n\r"
  "str_URL = \"rec=1\";}else{\n\r"
  "document.getElementById(\"rec\").style.display = \"none\";\n\r"
  "str_URL = \"rec=0\";}\n\r"
  "trans_par(id_cb);}\n\r"
  "var old_rid;\n\r"
  "function r_check(id_cb){\n\r"
  "if(id_cb.checked){ if (id_cb.id == old_rid){\n\r"
  "id_cb.checked=false;\n\r"
  "old_rid = \"\";\n\r"
  "document.getElementById(\"but_rec\").style.display =\"none\";\n\r"
  "document.getElementById(\"but_rec\").disabled;\n\r"
  "}else{\n\r"
  "document.getElementById(\"but_rec\").style.display =\"block\";\n\r"
  "document.getElementById(\"ex_input\").value = document.getElementById(\"ex_\"+id_cb.id).value; \n\r"
  "old_rid = id_cb.id;}}}\n\r"
  "function rec_edit(id_cb){\n\r"
  "var new_id;\n\r"
  "for (var i=1; i <5; i++){\n\r"
  "if (document.getElementById(\"r\"+String(i)).checked){\n\r"
  "new_id = \"ex_r\"+String(i);}}\n\r"
  "document.getElementById(new_id).value = document.getElementById(\"ex_input\").value;\n\r"
  "str_URL = new_id + \"=\" + document.getElementById(new_id).value;\n\r"
  "//alert(\"rec URL \" + str_URL);\n\r"
  "trans_par(id_cb);\n\r"
  "}\n\r"
  "</script></head>\n\r"
  "<body onload=\"startTimer()\">\n\r";

const static char header14[] PROGMEM =
  "<ul id=\"nav\">\n\r"
  "<li><a href=\"#\" class=\"sub\" tabindex=\"1\">select&nbsp;&nbsp;&nbsp;&nbsp;pult</a>\n\r"
  "<ul>\n\r"
  "<li style=\"text-align:center\"><a href=\"/macro\">MACRO</a></li>\n\r"
  "<li><a href=\"transponder\">transponder</a></li>\n\r"
  "<li><a href=\"mp3\">mp3</a></li>\n\r"
  "<li><a href=\"lg\">LG</a></li>\n\r"
  "<li><a href=\"panasonic\">PANASONIC</a></li>\n\r"
  "<li><a href=\"samsung\">SAMSUNG</a></li>\n\r"
  "<li><a href=\"/sony\">SONY</a></li>\n\r"
  "<li><a href=\"/grail\">G-Rail</a></li>\n\r"
  "<li><a href=\"/map\">map</a></li>\n\r"
  "<li><a href=\"/ESP8266\">ESP8266</a></li>\n\r"
  "<li><a href=\"/light1\">Light1</a></li>\n\r"
  "<li><a href=\"/light2\">Light2</a></li>\n\r"
  "<li><a href=\"/socket\">РОЗЕТКИ</a></li>\n\r"
  "<li style=\"text-align:center\"><a href=\"/daily\">DAILY</a></li>\n\r"
  "<li onclick=\"f_rec(this)\" style=\"text-align:center\"><a>SCAN</a> <span id= \"rec\" style=\"display: none; color:#F00; font-weight:bold\" align=\"float\">REC</span></li>\n\r"
  "</ul></li></ul><!--end-->\n\r";

const static char daily1[] PROGMEM =
  "<div class= \"ex_output\" style=\"width:258px\">\n\r"
  "<output id=\"ex_time\"><span>DevKit ESP8266</span></output>\n\r"
  "</div>\n\r"
  "<div style=\"background-color: #666; width:168px\">\n\r"
  "<div class= \"ex_output\" style=\"width:258px\">\n\r"
  "<output id=\"ex_out\"><span>schedule for the day</span></output>\n\r"
  "</div>\n\r"
  "<table id=\"pult\" title=\"daily\" width=\"260px\" border=\"0\">\n\r"
  "<tr><td style=\"color:#CCC; font-weight:bolder\">&nbsp;&nbsp;&nbsp;&nbsp;задание на день</td></tr>\n\r"
  "<tr><td><div class= \"ex_output\">\n\r"
  "<output id=\"ex_r1\"><span>time1</span></output>\n\r"
  "<input type=\"radio\" name=\"trad\" id=\"r1\" onClick=\"r_check(this)\">\n\r"
  "</div></td></tr>\n\r"
  "<tr><td><div class= \"ex_output\">\n\r"
  "<output id=\"ex_r2\"><span>time2</span></output>\n\r"
  "<input type=\"radio\" name=\"trad\" id=\"r2\" onClick=\"r_check(this)\">\n\r"
  "</div></td></tr>\n\r";

const static char daily2[] PROGMEM =
  "<tr><td><div class= \"ex_output\">\n\r"
  "<output id=\"ex_r3\"><span>time3</span></output>\n\r"
  "<input type=\"radio\" name=\"trad\" id=\"r3\" onClick=\"r_check(this)\">\n\r"
  "</div></td></tr>\n\r"
  "<tr><td><div class= \"ex_output\">\n\r"
  "<output id=\"ex_r4\"><span>time4</span></output>\n\r"
  "<input type=\"radio\" name=\"trad\" id=\"r4\" onClick=\"r_check(this)\">\n\r"
  "</div></td></tr>\n\r"
  "<tr>\n\r"
  "<td><div>\n\r"
  "<input type=\"text\" id=\"ex_input\" style=\"width:254px; text-align:center\">\n\r"
  "</div></td></tr>\n\r"
  "<tr><td align=\"center\"><div class=\"but\" id=\"but_rec\" style=\"background-color: #999; color: #F00; height:22px; width:120px;\" onclick=\"rec_edit(this)\" > <span style=\"margin-top:3px\" >записать</span> </div></td></tr>\n\r"
  "<tr><td>&nbsp;</td></tr>\n\r"
  "<tr><td style=\"color:#CCC; font-weight:bolder\">опрос каждые\n\r"
  "<input type=\"number\" min=\"1\" max=\"59\" step=\"1\" id=\"r_sec\" style=\"width:72; text-align:center\" value=\"30\"/>\n\r"
  "<span style=\"color:#006\">&nbsp;сек.</span>\n\r"
  "<input type=\"checkbox\" id=\"tt1\" onclick=\"my_timer(this)\" /><br />\n\r"
  "<span id=\"my_clock\" style=\"color: #eee;  font-weight: bold; margin-left:118px;\">00:10</span>\n\r"
  "</td></tr></table>\n\r"
  "</div><div id=\"ip\" title=\"#\"></div></body></html>\n\r";

const static char map1[] PROGMEM =
  "<table id=\"pult\" title=\"map\"><tbody>\n\r"
  "<tr><td colspan=\"4\" align=\"center\"><div class= \"ex_output\"  style=\"width:290px\">\n\r"
  "<output id=\"ex_out\"><span>DevKit ESP8266 Control</span></output>\n\r"
  "<input type=\"checkbox\" id=\"te2\" checked/></div></td></tr>\n\r"
  "<tr bgcolor=\"#CCCCCC\">\n\r"
  "<td align=\"center\"><strong>GPIO</strong></td>\n\r"
  "<td align=\"center\">&nbsp;</td>\n\r"
  "<td align=\"center\" style=\"background-color:#00C\"><span class=\"out_val\">\n\r"
  "<output id=\"result1\">0</output></span></td>\n\r"
  "<td align=\"center\"><strong>on/off</strong></td></tr>\n\r"
  "<tr><td width=\"53\" align=\"center\">16</td>\n\r"
  "<td width=\"34\">бел</td>\n\r"
  "<td width=\"148\"><input type=\"range\" onchange=\"polsun_val(this)\" class=\"rangeP\" id=\"cd16\" value=\"0\" min=\"0\" max=\"1023\"/></td>\n\r"
  "<td width=\"34\"><div class=\"onoffswitch\">\n\r"
  "<input type=\"checkbox\" class=\"onoffswitch-checkbox\" id=\"cb16\" onclick=\"cb_check(this)\"/>\n\r"
  "<label class=\"onoffswitch-label\" for=\"cb16\"> <span class=\"onoffswitch-inner2\"></span>\n\r"
  " <span class=\"onoffswitch-switch\"></span></label></div></td></tr>\n\r"
  "<tr bgcolor=\"#CCCCCC\"><td align=\"center\">5</td>\n\r"
  "<td>красн</td>\n\r"
  "<td><input type=\"range\" onchange=\"polsun_val(this)\" class=\"rangeP\" id=\"cd5\" value=\"0\" min=\"0\" max=\"1023\" /></td>\n\r"
  "<td><div class=\"onoffswitch\">\n\r"
  "<input type=\"checkbox\" class=\"onoffswitch-checkbox\" id=\"cb5\" onclick=\"cb_check(this)\"/>\n\r"
  "<label class=\"onoffswitch-label\" for=\"cb5\"> <span class=\"onoffswitch-inner2\"></span> <span class=\"onoffswitch-switch\"></span></label>\n\r"
  "</div></td></tr>\n\r"
  "<tr><td align=\"center\">4</td>\n\r"
  "<td>жел</td>\n\r"
  "<td><input type=\"range\" onchange=\"polsun_val(this)\" class=\"rangeP\" id=\"cd4\" value=\"0\" min=\"0\" max=\"1023\" /></td>\n\r"
  "<td><div class=\"onoffswitch\">\n\r"
  "<input type=\"checkbox\" class=\"onoffswitch-checkbox\" id=\"cb4\" onclick=\"cb_check(this)\"/>\n\r"
  "<label class=\"onoffswitch-label\" for=\"cb4\" > <span class=\"onoffswitch-inner2\"></span> \n\r"
  "<span class=\"onoffswitch-switch\"></span></label></div></td></tr>\n\r";

const static char map2[] PROGMEM =
  "<tr bgcolor=\"#CCCCCC\"><td align=\"center\">0</td>\n\r"
  "<td>син</td>\n\r"
  "<td><input type=\"range\" onchange=\"polsun_val(this)\" class=\"rangeP\" id=\"cd0\" value=\"0\" min=\"0\" max=\"1023\" /></td>\n\r"
  "<td><div class=\"onoffswitch\">\n\r"
  "<input type=\"checkbox\" class=\"onoffswitch-checkbox\" id=\"cb0\" onclick=\"cb_check(this)\" />\n\r"
  "<label class=\"onoffswitch-label\" for=\"cb0\"> <span class=\"onoffswitch-inner2\"></span> <span class=\"onoffswitch-switch\"></span></label>\n\r"
  "</div></td></tr>\n\r"
  "<tr><td align=\"center\">&nbsp;</td>\n\r"
  "<td>&nbsp;</td><td>&nbsp;</td><td>&nbsp;</td></tr>\n\r"
  "<tr bgcolor=\"#CCCCCC\"><td align=\"center\">12</td><td>avto</td>\n\r"
  "<td><input type=\"range\" onchange=\"polsun_val(this)\" class=\"rangeP\" id=\"cd12\" value=\"0\" min=\"0\" max=\"1023\" /></td>\n\r"
  "<td><div class=\"onoffswitch\">\n\r"
  "<input type=\"checkbox\" class=\"onoffswitch-checkbox\" id=\"cb12\" onclick=\"cb_check(this)\"/>\n\r"
  "<label class=\"onoffswitch-label\" for=\"cb12\"> <span class=\"onoffswitch-inner2\"></span>\n\r"
  "<span class=\"onoffswitch-switch\"></span></label></div></td></tr>\n\r"
  "<tr><td align=\"center\">13</td><td>temp</td>\n\r"
  "<td><input type=\"range\" onchange=\"polsun_val(this)\" class=\"rangeP\" id=\"cd13\" value=\"1\" min=\"1\" max=\"10\" /></td>\n\r"
  "<td><div class=\"onoffswitch\">\n\r"
  "<input type=\"checkbox\" class=\"onoffswitch-checkbox\" id=\"cb13\" onclick=\"cb_check(this)\"/>\n\r"
  "<label class=\"onoffswitch-label\" for=\"cb13\"> <span class=\"onoffswitch-inner2\"></span>\n\r"
  "<span class=\"onoffswitch-switch\"></span></label></div></td></tr>\n\r"
  "<tr bgcolor=\"#E10000\"><td>&nbsp;</td><td>&nbsp;</td><td>&nbsp;</td><td>&nbsp;</td></tr>\n\r"
  "</tbody></table><br />\n\r";

const static char map3[] PROGMEM =
  "<table border=\"1\"><tr><th width=\"92\" scope=\"col\">таймер (sec)</th>\n\r"
  "<th width=\"88\" scope=\"col\">опрос через</th>\n\r"
  "<th colspan=\"2\" scope=\"col\">розетки</th></tr>\n\r"
  "<tr><td><input type=\"checkbox\" id=\"tt1\" onclick=\"my_timer(this)\"  />\n\r"
  "<input style=\"width:52px; text-align:center\" type=\"number\" min=\"1\" max=\"59\" step=\"1\" id=\"r_sec\" value=\"4\" /></td>\n\r"
  "<td><input style=\"width:52px; text-align:center\" type=\"number\" min=\"1\" max=\"59\" step=\"1\" id=\"r_sec2\" value=\"2\" />сек.</td>\n\r"
  "<td width=\"75\"><div class=\"onoffswitch\" style=\"padding-left:32px;\" >\n\r"
  "<input type=\"checkbox\" name=\"192.168.0.120\" class=\"onoffswitch-checkbox\" id=\"vv12\" onclick=\"cb_check0(this)\" />\n\r"
  "<label class=\"onoffswitch-label\" for=\"vv12\"> <span class=\"onoffswitch-inner2\"></span>\n\r"
  "<span class=\"onoffswitch-switch\"></span></label></div></td>\n\r"
  "<td width=\"11\">1</td></tr>\n\r"
  "<tr><td align=\"center\"><span id=\"my_clock\" style=\"color: #f00;  font-weight: bold;\" >00:08</span></td>\n\r"
  "<td>&nbsp;</td><td><div class=\"onoffswitch\"style=\"padding-left:32px;\" >\n\r"
  "<input type=\"checkbox\" name=\"192.168.0.130\" class=\"onoffswitch-checkbox\" id=\"vv13\" onclick=\"cb_check0(this)\" />\n\r"
  "<label class=\"onoffswitch-label\" for=\"vv13\"> <span class=\"onoffswitch-inner2\"></span>\n\r"
  "<span class=\"onoffswitch-switch\"></span></label></div></td>\n\r"
  "<td>2</td></tr></table>\n\r"
  "<div style=\"width:294px; background-color:#C10000; height:4px\" >&nbsp;</div>\n\r"
  "<div id=\"ip\" title=\"192.168.0.121\"></div></body></html>\n\r";


const static char samsung1[] PROGMEM =
  "<table id=\"pult\" title=\"SAMSUNG\" width=\"176\" border=\"0\"><tr>\n\r"
  "<td width=\"56\"><div class=\"but\" id=cross style=\"background-color:#0C6; color:#fff\" onclick=\"sel_but(this)\"><span>&nbsp;</span></div></td>\n\r"
  "<td width=\"55\"><div class=\"key\" id=src onclick=\"sel_but(this)\"><span>SRC</span></div></td>\n\r"
  "<td width=\"51\"><div class=\"but\" id=pwr style=\"background-color: #E20E0E; color:#fff\" onclick=\"sel_but(this)\"><span>PWR</span></div></td></tr>\n\r";

const static char samsung2[] PROGMEM =
  "<tr><td><div class=\"key\" id=1 onclick=\"sel_but(this)\"><span>1</span></div></td>\n\r"
  "<td><div class=\"key\" id=2 onclick=\"sel_but(this)\" ><span>2</span></div></td>\n\r"
  "<td><div class=\"key\" id=3 onclick=\"sel_but(this)\" ><span>3</span></div></td></tr>\n\r"
  "<tr><td><div class=\"key\" id=4 onclick=\"sel_but(this)\" ><span>4</span></div></td>\n\r"
  "<td><div class=\"key\" id=5 onclick=\"sel_but(this)\" ><span>5</span></div></td>\n\r"
  "<td><div class=\"key\" id=6 onclick=\"sel_but(this)\" ><span>6</span></div></td></tr>\n\r"
  "<tr><td><div class=\"key\" id=7 onclick=\"sel_but(this)\" ><span>7</span></div></td>\n\r"
  "<td><div class=\"key\" id=8 onclick=\"sel_but(this)\" ><span>8</span></div></td>\n\r"
  "<td><div class=\"key\" id=9 onclick=\"sel_but(this)\" ><span>9</span></div></td></tr>\n\r"
  "<tr><td><div class=\"key\" id=ttx onclick=\"sel_but(this)\" ><span>TTX/<br />\n\r"
  "MIX</span></div></td>\n\r"
  "<td><div class=\"key\" id=0 onclick=\"sel_but(this)\" ><span>0</span></div></td>\n\r"
  "<td><div class=\"key\" id=pre onclick=\"sel_but(this)\" ><span>PRE</span></div></td></tr>\n\r"
  "<tr><td>&nbsp;</td>\n\r"
  "<td>&nbsp;</td>\n\r"
  "<td>&nbsp;</td></tr>\n\r"
  "<tr><td bgcolor=\"#666666\"><div class=\"key\" id=\"voladd\" onclick=\"sel_but(this)\"><span style=\"font-size:16px\">+</span></div></td>\n\r"
  "<td><div class=\"key\" id=mute onclick=\"sel_but(this)\"><span>mute</span></div></td>\n\r"
  "<td bgcolor=\"#666666\"><div class=\"key\" id=\"canadd\" onclick=\"sel_but(this)\"><span style=\"font-size:16px\">+</span></div></td></tr>\n\r";

const static char samsung3[] PROGMEM =
  "<tr><td bgcolor=\"#666666\" style=\"text-align:center\">volume</td>\n\r"
  "<td>&nbsp;</td>\n\r"
  "<td bgcolor=\"#666666\" style=\"text-align:center\">canal</td></tr>\n\r"
  "<tr><td bgcolor=\"#666666\"><div class=\"key\" id=volsub onclick=\"sel_but(this)\"><span style=\"font-size:16px\">-</span></div></td>\n\r"
  "<td><div class=\"key\" id=list onclick=\"sel_but(this)\"><span>list</span></div></td>\n\r"
  "<td bgcolor=\"#666666\"><div class=\"key\" id=cansub onclick=\"sel_but(this)\"><span style=\"font-size:16px\">-</span></div></td></tr>\n\r"
  "<tr><td>&nbsp;</td><td>&nbsp;</td><td>&nbsp;</td></tr>\n\r"
  "<tr><td><div class=\"but\" id=menu onclick=\"sel_but(this)\"><span style=\"font-size:10px\">menu</span></div></td>\n\r"
  "<td><div class=\"key\" id=key_up onclick=\"sel_but(this)\"><span>&#x25B2;</span></div></td>\n\r"
  "<td><div class=\"but\" id=info onclick=\"sel_but(this)\"><span style=\"font-size:10px\">info</span></div></td></tr>\n\r"
  "<tr><td><div class=\"key\" id=key_left onclick=\"sel_but(this)\"><span>&#x25C0;</span></div></td>\n\r"
  "<td><div class=\"but\" id=ok onclick=\"sel_but(this)\"><span>Ok</span></div></td>\n\r"
  "<td><div class=\"key\" id=key_right onclick=\"sel_but(this)\"><span>&#x25B6;</span></div></td></tr>\n\r"
  "<tr><td><div class=\"but\" id=return onclick=\"sel_but(this)\"><span style=\"font-size:10px\">return</span></div></td>\n\r"
  "<td><div class=\"key\" id=key_down onclick=\"sel_but(this)\"><span>&#x25BC;</span></div></td>\n\r"
  "<td><div class=\"but\" id=exit onclick=\"sel_but(this)\"><span style=\"font-size:10px\">exit</span></div></td></tr>\n\r"
  "</table>\n\r"
  "<table width=\"176\" border=\"0\">\n\r"
  "<tr><th scope=\"col\"><div class=\"but\" id=a onclick=\"sel_but(this)\" style=\"height:10px; width:29px; background-color:#F00\">&nbsp;</div></th>\n\r"
  "<th scope=\"col\"><div class=\"but\" id=b onclick=\"sel_but(this)\" style=\"height:10px; width:29px; background-color: #396\">&nbsp;</div></th>\n\r"
  "<th scope=\"col\"><div align=\"left\" class=\"but\" id=c onclick=\"sel_but(this)\" style=\"height:10px; width:29px; background-color: #FF0\">&nbsp;</div></th>\n\r"
  "<th scope=\"col\"><div class=\"but\" id=d onclick=\"sel_but(this)\" style=\"height:10px; width:29px; background-color: #03F\">&nbsp;</div></th></tr>\n\r"
  "</table><div class=\"cleared\"></div>\n\r"
  "<div class= \"ex_pult\">\n\r"
  "<output id=\"pultname\"><span>wait</span></output></div>\n\r"
  "<div id=\"ip\" title=\"#\"></div>\n\r"
  "</body></html>\n\r";

const static char light1_1[] PROGMEM =
  "<table id=\"pult\" title=\"light1\" width=\"240\" border=\"0\"><tr>\n\r"
  "<td width=\"54\"><div class=\"but\" id=up onclick=\"sel_but(this)\" ><span>&#x25B2;</span></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=down onclick=\"sel_but(this)\"><span>&#x25BC;</span></div></td>\n\r"
  "<td width=\"54\" style=\"background-color: #666\"><div class=\"but\" id=play onclick=\"sel_but(this)\" style=\"background-color:  #444; color:#fff\"><span>&#x25B6;II</span></div></td>\n\r"
  "<td width=\"54\" style=\"background-color: #666\"><div class=\"but\" id=pwr onclick=\"sel_but(this)\" style=\"background-color:  #E20E0E; color:#fff\"><span>PWR</span></div></td>\n\r"
  "</tr></table>\n\r";

const static char light1_2[] PROGMEM =
  "<table width=\"240\" border=\"0\" bgcolor=\"#999999\"><tr>\n\r"
  "<td width=\"54\"><div class=\"but\" id=red onclick=\"sel_but(this)\" style=\"background-color:  #E20E0E; color:#fff\" ><span>RED</span></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=green onclick=\"sel_but(this)\" style=\"background-color:  #00eE00\" ><span>GREEN</span></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=blue onclick=\"sel_but(this)\" style=\"background-color:  #0000ee; color:#fff\"><span>BLUE</span></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=white onclick=\"sel_but(this)\" style=\"background-color:  #fff; color:#444\"><span>WHITE</span></div></td></tr>\n\r"
  "<tr><td width=\"54\"><div class=\"but\" id=orange onclick=\"sel_but(this)\" style=\"background-color: #FC0; color:#444\"><span>ORANGE</span></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=yellow onclick=\"sel_but(this)\" style=\"background-color: #FF0; color:#444\"><span>YELLOW</span></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=cyan onclick=\"sel_but(this)\" style=\"background-color: #3CC; color:#fff\"><span>CYAN</span></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=purple onclick=\"sel_but(this)\" style=\"background-color: #90C; color:#fff\"><span>PURPLE</span></div></td></tr></table>\n\r";

const static char light1_3[] PROGMEM =
  "<table width=\"240\" border=\"0\" bordercolor=\"#FFFFFF\"><tr>\n\r"
  "<td width=\"54\"><div class=\"but\" id=auto onclick=\"sel_but(this)\" ><span>AUTO</span></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=jump3 onclick=\"sel_but(this)\" ><span>jump3</span></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=fade3 onclick=\"sel_but(this)\" ><span>FADE3</span></div></td>\n\r"
  "<td width=\"51\" style=\"background-color: #666\"><div class=\"but\" id=s_add onclick=\"sel_but(this)\" ><span>+<br />\n\r"
  "SPEED </span></div></td></tr><tr>\n\r"
  "<td width=\"54\"><div class=\"but\" id=flash onclick=\"sel_but(this)\" ><span>Flash</span></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=jump7 onclick=\"sel_but(this)\" ><span>jump7</span></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=fade7 onclick=\"sel_but(this)\" ><span>FADE7</span></div></td>\n\r"
  "<td width=\"54\" style=\"background-color: #666\"><div class=\"but\" id=s_sub onclick=\"sel_but(this)\" ><span>-<br />\n\r"
  "SPEED </span></div></td></tr></table>\n\r"
  "<div class=\"cleared\"></div><div style=\"background-color:#909; width:240px; height:4px;\"> </div>\n\r"
  "<div id=\"ip\" title=\"#\"></div>\n\r"
  "<p>&nbsp;</p></body></html>\n\r";


const static char light2_0[] PROGMEM =
  // "<body style=\"width:240px\">\n\r"
  "<table id=\"pult\" title=\"light2\" width=\"240\" border=\"0\"><tr>\n\r"
  "<tr><td width=\"54\"><div class=\"but\" id=but_001 onclick=\"sel_but(this)\" ></div></td>\n\r"
  "<td width=\"54\">&nbsp;</td>\n\r"
  "<td width=\"54\">&nbsp;</td>\n\r"
  "<td width=\"54\" style=\"background-color: #666\"><div class=\"but\" id=pwr onclick=\"sel_but(this)\" style=\"background-color:  #E20E0E; color:#fff\"><span>PWR</span></div></td>\n\r"
  "</tr></table>\n\r";

const static char light2_1[] PROGMEM =
  "<table width=\"240\" border=\"0\" bgcolor=\"#999999\">\n\r"
  "<tr><td width=\"54\"><div class=\"but\" id=red onclick=\"sel_but(this)\" style=\"background-color:  #E20E0E; color:#fff\" ></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=green onclick=\"sel_but(this)\" style=\"background-color:  #00eE00\" ></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=blue onclick=\"sel_but(this)\" style=\"background-color:  #0000ee; color:#fff\"></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=white onclick=\"sel_but(this)\" style=\"background-color: #FFC; color:#444\"></div></td></tr>\n\r"
  "<tr><td width=\"54\"><div class=\"but\" id=orange onclick=\"sel_but(this)\" style=\"background-color: #FC0; color:#444\"></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=yellow onclick=\"sel_but(this)\" style=\"background-color: #FF0; color:#444\"></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=cian onclick=\"sel_but(this)\" style=\"background-color: #3CC; color:#fff\"></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=purple onclick=\"sel_but(this)\" style=\"background-color: #90C; color:#fff\"></div></td></tr></table>\n\r";

const static char light2_2[] PROGMEM =
  "<table width=\"240\" border=\"0\" bordercolor=\"#FFFFFF\">\n\r"
  "<tr><td width=\"54\"><div class=\"but\" id=flash onclick=\"sel_but(this)\" ><span style=\"font-size:9px\">FLASH</span></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=bloom onclick=\"sel_but(this)\" ><span style=\"font-size:9px\">BLOOM</span></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=smooth onclick=\"sel_but(this)\" ><span style=\"font-size:9px\">SMOOTH</span></div></td>\n\r"
  "<td width=\"51\" style=\"background-color: #666\"><div class=\"but\" id=s_add onclick=\"sel_but(this)\" ><span>+<br />\n\r"
  "SPEED</span></div></td></tr>\n\r"
  "<tr><td width=\"54\"><div class=\"but\" id=ft onclick=\"sel_but(this)\" ><span>f.t.</span></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=mt onclick=\"sel_but(this)\" ><span>m.t.</span></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=ct onclick=\"sel_but(this)\" ><span>c.t.</span></div></td>\n\r"
  "<td width=\"54\" style=\"background-color: #666\"><div class=\"but\" id=s_sub onclick=\"sel_but(this)\" ><span>-<br />SPEED</span></div></td>\n\r"
  "</tr></table>\n\r"
  "<div class=\"cleared\"></div><div style=\"background-color:#099; width:240px; height:4px;\"> </div>\n\r"
  "<div id=\"ip\" title=\"#\"></div>\n\r"
  "<p>&nbsp;</p></body></html>\n\r";


const static char grail0[] PROGMEM =
  "<div id=\"ip\" title=\"#\"></div>\n\r"
  "<table id=\"pult\" title=\"grail\" width=\"176\" border=\"0\" bgcolor=\"#999999\">\n\r"
  "<tr><td width=\"54\"><div class=\"but\" id=set onclick=\"sel_but(this)\" ><span>time</span></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=prog onclick=\"sel_but(this)\" ><span>P</span></div></td>\n\r"
  "<td width=\"54\"><div class=\"but\" id=reset onclick=\"sel_but(this)\" ><span>time</span></div></td>\n\r"
  "</tr><tr align=\"center\" style=\"color:#eee\">\n\r"
  "<td>set</td><td>&nbsp;</td><td>reset</td></tr><tr align=\"center\" style=\"color:#eee\"><td>open</td>\n\r"
  "<td>&nbsp;</td><td>close</td></tr>\n\r"
  "<tr><td width=\"54\"><div class=\"key\" id=open onclick=\"sel_but(this)\" ><span>open</span></div></td>\n\r"
  "<td width=\"54\">&nbsp;</td><td width=\"54\"><div class=\"key\" id=close onclick=\"sel_but(this)\" ><span>close</span></div></td></tr></table>\n\r"
  "<div align=\"center\" style=\"width:176px\"><h4>G-RAIL</h4></div>\n\r"
  "<div class=\"cleared\"></div><div style=\"background-color:#009; width:176px; height:4px;\"> </div>\n\r"
  "<div id=\"ip\" title=\"#\"></div>\n\r"
  "<p>&nbsp;</p></body></html>\n\r";

const static char esp0[] PROGMEM =
  //  "<body onload=\"startTimer()\">\n\r"
  "<div class= \"ex_output\"><output id=\"ex_out\"><span>DevKit ESP8266 Control v1.03</span></output></div>\n\r"
  "<table id=\"pult\" title=\"ESP8266\" border=\"0\">\n\r"
  "<tr><td width=\"86\"><div class=\"output\"><div><span>Can 1 = A0</span></div><output id=\"out1\">0</output></div></td>\n\r"
  "<td width=\"86\"><div class=\"output\"><div><span>Can 2</span></div><output id=\"out2\">0</output></div></td>\n\r"
  "<td width=\"86\"><div class=\"output\"><div><span>Can 3</span></div><output id=\"out3\">0</output></div></td>\n\r"
  "</tr><tr><td>&nbsp;&nbsp;<input type=\"number\" min=\"1\" max=\"59\" step=\"1\" id=\"r_sec\" style=\"width:72; text-align:center\" value=\"5\"/></td>\n\r"
  "<td><input type=\"checkbox\" id=\"tt1\" onclick=\"my_timer(this)\" /> avto</td>\n\r"
  "<td align=\"center\"><span id=\"my_clock\" style=\"color: #f00;  font-weight: bold;\">00:10</span></td></tr>\n\r"
  "<tr align=\"center\">\n\r"
  "<td><input type=\"button\" id=\"52\" value=\"PWM\" onclick=\"return toggleView(this, 'data-table', 'td')\" style=\"width:64px\" /></td>\n\r"
  "<td>output\n\r"
  "<input type=\"checkbox\" id=\"ce1\" onclick=\"cb_check0(this)\" checked/></td>\n\r"
  "<td><input type=\"button\" id=\"522\" value=\"mode\" onclick=\"return toggleView_mode(this, 'data-table', 'td')\" style=\"width:64px\" /></td></tr></table>\n\r";

const static char esp1[] PROGMEM =
  "<table id=\"data-table\"><tbody>\n\r"
  "<tr bgcolor=\"#CCCCCC\"><td align=\"center\"><strong>on/off</strong></td>\n\r"
  "<td align=\"center\" class=\"column_view-m column_view-off\" style=\"background-color:#00C\">&nbsp;</td>\n\r"
  "<td align=\"center\" class=\"mode_view-m mode_view-off\"><strong>i</strong></td>\n\r"
  "<td align=\"center\" class=\"mode_view-m mode_view-off\"><strong>mode</strong></td>\n\r"
  "<td align=\"center\"><strong>pin</strong></td><td align=\"center\"><strong>instr</strong></td>\n\r"
  "<td align=\"center\" bgcolor=\"#666666\">&nbsp;</td><td align=\"center\"><strong>instr</strong></td>\n\r"
  "<td align=\"center\"><strong>pin</strong></td>\n\r"
  "<td align=\"center\" class=\"mode_view-m mode_view-off\"><strong>mode</strong></td>\n\r"
  "<td align=\"center\" class=\"mode_view-m mode_view-off\"><strong>i</strong></td>\n\r"
  "<td align=\"center\" class=\"column_view-m column_view-off\" style=\"background-color:#00C\"><span class=\"out_val\">\n\r"
  "<output id=\"result1\">0</output></span></td><td align=\"center\"><strong>on/off</strong></td></tr>\n\r";

const static char esp2[] PROGMEM =
  "<tr>\n\r"
  "<td width=\"38\">\n\r"
  "<div class=\"onoffswitch\"><input type=\"checkbox\" class=\"onoffswitch-checkbox\" id=\"cbA0\" onclick=\"cb_check(this)\" >\n\r"
  "<label class=\"onoffswitch-label\" for=\"cbA0\"> <span class=\"onoffswitch-inner\"></span> <span class=\"onoffswitch-switch2\"></span> </label>\n\r"
  "</div></td>\n\r"
  "<td width=\"148\" class=\"column_view-m column_view-off\">\n\r"
  "<input type=\"range\" onchange=\"polsun_val(this)\" class=\"rangeP\" id=\"cdA0\" value=\"0\" max=\"1023\" min=\"0\"/></td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">&nbsp;</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">\n\r"
  "<select id=\"sdA0\" onchange=\"sel_test(this)\"><option  style=\"background-color: #d9fffe\">Input</option>\n\r"
  "<option style=\"background-color: #fffed9\" selected=\"selected\">Output</option></select></td>\n\r"
  "<td width=\"36\">A0</td>\n\r"
  "<td width=\"53\">ADC0</td>\n\r"
  "<td width=\"1\" bgcolor=\"#666666\">&nbsp;</td>\n\r"
  "<td width=\"53\">GPIO16</td>\n\r"
  "<td width=\"34\">D0</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\"><select id=\"sd16\" onchange=\"sel_test(this)\">\n\r"
  "<option  style=\"background-color: #d9fffe\"  selected=\"selected\">Input</option><option style=\"background-color: #fffed9\" selected=\"selected\">Output</option></select></td>\n\r"
  "<td class=\"mode_view-m mode_view-off\"><input type=\"checkbox\" id=\"ci16\" onclick=\"cb_check0(this)\" disabled=\"disabled\"/></td>\n\r"
  "<td align=\"center\" class=\"column_view-m column_view-off\" width=\"148\">\n\r"
  "<input type=\"range\" onchange=\"polsun_val(this)\" class=\"rangeP\" id=\"cd16\" value=\"0\" max=\"1023\" min=\"0\" /></td>\n\r"
  "<td width=\"34\">\n\r"
  "<div class=\"onoffswitch\"><input type=\"checkbox\" class=\"onoffswitch-checkbox\" id=\"cb16\" onclick=\"cb_check(this)\"/>\n\r"
  "<label class=\"onoffswitch-label\" for=\"cb16\"> <span class=\"onoffswitch-inner2\"></span> <span class=\"onoffswitch-switch\"></span></label></div></td>\n\r"
  "</tr>\n\r";

const static char esp3[] PROGMEM =
  "<tr bgcolor=\"#CCCCCC\"><td>&nbsp;</td>\n\r"
  "<td class=\"column_view-m column_view-off\">&nbsp;</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">&nbsp;</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">&nbsp;</td>\n\r"
  "<td>RSV</td><td>reserv</td><td bgcolor=\"#666666\">&nbsp;</td>\n\r"
  "<td>GPIO5</td><td>D1</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\"><select id=\"sd5\" onchange=\"sel_test(this)\">\n\r"
  "<option  style=\"background-color: #d9fffe\">Input</option><option style=\"background-color: #fffed9\" selected=\"selected\">Output</option></select></td>\n\r"
  "<td class=\"mode_view-m mode_view-off\"><input type=\"checkbox\" id=\"ci5\" onclick=\"cb_check0(this)\" /></td>\n\r"
  "<td align=\"center\" class=\"column_view-m column_view-off\">\n\r"
  "<input type=\"range\" onchange=\"polsun_val(this)\" class=\"rangeP\" id=\"cd5\" value=\"0\" max=\"1023\" min=\"0\" /></td>\n\r"
  "<td>\n\r"
  "<div class=\"onoffswitch\"><input type=\"checkbox\" class=\"onoffswitch-checkbox\" id=\"cb5\" onclick=\"cb_check(this)\"/>\n\r"
  "<label class=\"onoffswitch-label\" for=\"cb5\"><span class=\"onoffswitch-inner2\"></span> <span class=\"onoffswitch-switch\"></span></label>\n\r"
  "</div></td>\n\r"
  "</tr>\n\r";

const static char esp4[] PROGMEM =
  "<tr>\n\r"
  "<td>&nbsp;</td>\n\r"
  "<td class=\"column_view-m column_view-off\">&nbsp;</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">&nbsp;</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">&nbsp;</td>\n\r"
  "<td>RSV</td>\n\r"
  "<td>reserv</td>\n\r"
  "<td bgcolor=\"#666666\">&nbsp;</td>\n\r"
  "<td>GPIO4</td><td>D2</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">\n\r"
  "<select id=\"sd4\" onchange=\"sel_test(this)\"><option  style=\"background-color: #d9fffe\">Input</option><option style=\"background-color: #fffed9\" selected=\"selected\">Output</option></select></td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">\n\r"
  "<input type=\"checkbox\" id=\"ci4\" onclick=\"cb_check0(this)\" /></td>\n\r"
  "<td align=\"center\" class=\"column_view-m column_view-off\">\n\r"
  "<input type=\"range\" onchange=\"polsun_val(this)\" class=\"rangeP\" id=\"cd4\" value=\"0\" max=\"1023\" min=\"0\" /></td>\n\r"
  "<td><div class=\"onoffswitch\"><input type=\"checkbox\" class=\"onoffswitch-checkbox\" id=\"cb4\" onclick=\"cb_check(this)\"/>\n\r"
  "<label class=\"onoffswitch-label\" for=\"cb4\" > <span class=\"onoffswitch-inner2\"></span> <span class=\"onoffswitch-switch\"></span></label>\n\r"
  "</div></td>\n\r"
  "</tr>\n\r";

const static char esp5[] PROGMEM =
  "<tr bgcolor=\"#CCCCCC\">\n\r"
  "<td><div class=\"onoffswitch\"><input type=\"checkbox\" class=\"onoffswitch-checkbox\" id=\"cb10\" onclick=\"cb_check(this)\" />\n\r"
  "<label class=\"onoffswitch-label\" for=\"cb10\"> <span class=\"onoffswitch-inner\"></span> <span class=\"onoffswitch-switch2\"></span> </label>\n\r"
  "</div></td>\n\r"
  "<td class=\"column_view-m column_view-off\"><input type=\"range\" onchange=\"polsun_val(this)\" class=\"rangeP\" id=\"cd10\" value=\"0\" max=\"1023\" min=\"0\"/></td>\n\r"
  "<td class=\"mode_view-m mode_view-off\"><input type=\"checkbox\" id=\"ci10\" onclick=\"cb_check0(this)\" /></td>\n\r"
  "<td class=\"mode_view-m mode_view-off\"><select id=\"sd10\" onchange=\"sel_test(this)\">\n\r"
  "<option  style=\"background-color: #d9fffe\">Input</option>\n\r"
  "<option style=\"background-color: #fffed9\" selected=\"selected\">Output</option></select></td>\n\r"
  "<td>SD3</td>\n\r"
  "<td>GPIO10</td>\n\r"
  "<td bgcolor=\"#666666\">&nbsp;</td>\n\r"
  "<td>GPIO0</td>\n\r"
  "<td>D3</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\"><select id=\"sd0\" onchange=\"sel_test(this)\">\n\r"
  "<option  style=\"background-color: #d9fffe\">Input</option>\n\r"
  "<option style=\"background-color: #fffed9\" selected=\"selected\">Output</option></select></td>\n\r"
  "<td class=\"mode_view-m mode_view-off\"><input type=\"checkbox\" id=\"ci0\" onclick=\"cb_check0(this)\" /></td>\n\r"
  "<td align=\"center\" class=\"column_view-m column_view-off\"><input type=\"range\" onchange=\"polsun_val(this)\" class=\"rangeP\" id=\"cd0\" value=\"0\" max=\"1023\" min=\"0\" /></td>\n\r"
  "<td><div class=\"onoffswitch\"><input type=\"checkbox\" class=\"onoffswitch-checkbox\" id=\"cb0\" onclick=\"cb_check(this)\" />\n\r"
  "<label class=\"onoffswitch-label\" for=\"cb0\"> <span class=\"onoffswitch-inner2\"></span> <span class=\"onoffswitch-switch\"></span></label>\n\r"
  "</div></td></tr>\n\r";

const static char esp6[] PROGMEM =
  "<tr>\n\r"
  "<td><div class=\"onoffswitch\"><input type=\"checkbox\" class=\"onoffswitch-checkbox\" id=\"cb9\" onclick=\"cb_check(this)\" />\n\r"
  "<label class=\"onoffswitch-label\" for=\"cb9\"> <span class=\"onoffswitch-inner\"></span> <span class=\"onoffswitch-switch2\"></span> </label>\n\r"
  "</div></td>\n\r"
  "<td class=\"column_view-m column_view-off\"><input type=\"range\" onchange=\"polsun_val(this)\" class=\"rangeP\" id=\"cd9\" value=\"0\" max=\"1023\" min=\"0\"/></td>\n\r"
  "<td class=\"mode_view-m mode_view-off\"><input type=\"checkbox\" id=\"ci9\" onclick=\"cb_check0(this)\" /></td>\n\r"
  "<td class=\"mode_view-m mode_view-off\"><select id=\"sd9\" onchange=\"sel_test(this)\">\n\r"
  "<option  style=\"background-color: #d9fffe\">Input</option>\n\r"
  "<option style=\"background-color: #fffed9\" selected=\"selected\">Output</option></select></td>\n\r"
  "<td>SD2</td>\n\r"
  "<td>GPIO9</td>\n\r"
  "<td bgcolor=\"#666666\">&nbsp;</td>\n\r"
  "<td>GPIO2</td><td>D4</td><td class=\"mode_view-m mode_view-off\"><select id=\"sd2\" onchange=\"sel_test(this)\">\n\r"
  "<option  style=\"background-color: #d9fffe\">Input</option>\n\r"
  "<option style=\"background-color: #fffed9\" selected=\"selected\">Output</option></select></td>\n\r"
  "<td class=\"mode_view-m mode_view-off\"><input type=\"checkbox\" id=\"ci2\" onclick=\"cb_check0(this)\" /></td>\n\r"
  "<td align=\"center\" class=\"column_view-m column_view-off\"><input type=\"range\" onchange=\"polsun_val(this)\" class=\"rangeP\" id=\"cd2\" value=\"0\" max=\"1023\" min=\"0\" /></td>\n\r"
  "<td><div class=\"onoffswitch\"><input type=\"checkbox\" class=\"onoffswitch-checkbox\" id=\"cb2\" onclick=\"cb_check(this)\"/>\n\r"
  "<label class=\"onoffswitch-label\" for=\"cb2\"><span class=\"onoffswitch-inner2\"></span> <span class=\"onoffswitch-switch\"></span></label>\n\r"
  "</div></td>\n\r"
  "</tr>\n\r";

const static char esp7[] PROGMEM =
  "<tr bgcolor=\"#CCCCCC\"><td>&nbsp;</td>\n\r"
  "<td class=\"column_view-m column_view-off\">&nbsp;</td>\n\r"
  "<td align=\"center\" class=\"mode_view-m mode_view-off\">i</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">&nbsp;</td>\n\r"
  "<td>SD1</td><td>MOSI</td><td bgcolor=\"#666666\">&nbsp;</td><td>&nbsp;</td><td bgcolor=\"#E10000\">3.3v</td><td class=\"mode_view-m mode_view-off\">&nbsp;</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">&nbsp;</td>\n\r"
  "<td align=\"center\" class=\"column_view-m column_view-off\">&nbsp;</td><td>&nbsp;</td></tr>\n\r"
  "<tr><td>&nbsp;</td>\n\r"
  "<td class=\"column_view-m column_view-off\">&nbsp;</td>\n\r"
  "<td align=\"center\" class=\"mode_view-m mode_view-off\">n</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">&nbsp;</td><td>CMD</td><td>CS</td>\n\r"
  "<td bgcolor=\"#666666\">&nbsp;</td><td>&nbsp;</td><td>GND</td><td class=\"mode_view-m mode_view-off\">&nbsp;</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">&nbsp;</td>\n\r"
  "<td align=\"center\" class=\"column_view-m column_view-off\">&nbsp;</td>\n\r"
  "<td>&nbsp;</td>\n\r"
  "</tr>\n\r";

const static char esp8[] PROGMEM =
  "<tr bgcolor=\"#CCCCCC\">\n\r"
  "<td>&nbsp;</td>\n\r"
  "<td class=\"column_view-m column_view-off\">&nbsp;</td>\n\r"
  "<td align=\"center\" class=\"mode_view-m mode_view-off\">t</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">&nbsp;</td>\n\r"
  "<td>SD0</td><td>MISO</td>\n\r"
  "<td bgcolor=\"#666666\">&nbsp;</td>\n\r"
  "<td>GPIO14</td>\n\r"
  "<td>D5</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\"><select id=\"sd14\" onchange=\"sel_test(this)\">\n\r"
  "<option  style=\"background-color: #d9fffe\">Input</option><option style=\"background-color: #fffed9\" selected=\"selected\">Output</option></select></td>\n\r"
  "<td class=\"mode_view-m mode_view-off\" width=\"20\"><input type=\"checkbox\" id=\"ci14\" onclick=\"cb_check0(this)\" /></td>\n\r"
  "<td align=\"center\" class=\"column_view-m column_view-off\"><input type=\"range\" onchange=\"polsun_val(this)\" class=\"rangeP\" id=\"cd14\" value=\"0\" max=\"1023\" min=\"0\" /></td>\n\r"
  "<td width=\"34\"><div class=\"onoffswitch\"><input type=\"checkbox\" class=\"onoffswitch-checkbox\" id=\"cb14\" onclick=\"cb_check(this)\"/>\n\r"
  "<label class=\"onoffswitch-label\" for=\"cb14\"> <span class=\"onoffswitch-inner2\"></span> <span class=\"onoffswitch-switch\"></span></label>\n\r"
  "</div></td>\n\r"
  "</tr>\n\r";

const static char esp9[] PROGMEM =
  "<tr>\n\r"
  "<td>&nbsp;</td>\n\r"
  "<td class=\"column_view-m column_view-off\">&nbsp;</td>\n\r"
  "<td align=\"center\" class=\"mode_view-m mode_view-off\">e</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">&nbsp;</td>\n\r"
  "<td>CLK</td><td>SCLK</td>\n\r"
  "<td bgcolor=\"#666666\">&nbsp;</td>\n\r"
  "<td>GPIO12</td>\n\r"
  "<td>D6</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\"><select id=\"sd12\" onchange=\"sel_test(this)\">\n\r"
  "<option  style=\"background-color: #d9fffe\">Input</option><option style=\"background-color: #fffed9\" selected=\"selected\">Output</option></select></td>\n\r"
  "<td class=\"mode_view-m mode_view-off\"><input type=\"checkbox\" id=\"ci12\" onclick=\"cb_check0(this)\" /></td>\n\r"
  "<td align=\"center\" class=\"column_view-m column_view-off\"><input type=\"range\" onchange=\"polsun_val(this)\" class=\"rangeP\" id=\"cd12\" value=\"0\" max=\"1023\" min=\"0\" /></td>\n\r"
  "<td><div class=\"onoffswitch\"><input type=\"checkbox\" class=\"onoffswitch-checkbox\" id=\"cb12\" onclick=\"cb_check(this)\"/><label class=\"onoffswitch-label\" for=\"cb12\">\n\r"
  "<span class=\"onoffswitch-inner2\"></span> <span class=\"onoffswitch-switch\"></span></label>\n\r"
  "</div></td>\n\r"
  "</tr>\n\r";

const static char esp10[] PROGMEM =
  "<tr bgcolor=\"#CCCCCC\">\n\r"
  "<td>&nbsp;</td>\n\r"
  "<td class=\"column_view-m column_view-off\">&nbsp;</td>\n\r"
  "<td align=\"center\" class=\"mode_view-m mode_view-off\">r</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">&nbsp;</td>\n\r"
  "<td>GND</td>\n\r"
  "<td>&nbsp;</td>\n\r"
  "<td bgcolor=\"#666666\">&nbsp;</td>\n\r"
  "<td>GPIO13</td><td>D7</td><td class=\"mode_view-m mode_view-off\"><select id=\"sd13\" onchange=\"sel_test(this)\">\n\r"
  "<option  style=\"background-color: #d9fffe\">Input</option><option style=\"background-color: #fffed9\" selected=\"selected\">Output</option></select></td>\n\r"
  "<td class=\"mode_view-m mode_view-off\"><input type=\"checkbox\" id=\"ci13\" onclick=\"cb_check0(this)\" /></td>\n\r"
  "<td align=\"center\" class=\"column_view-m column_view-off\"><input type=\"range\" onchange=\"polsun_val(this)\" class=\"rangeP\" id=\"cd13\" value=\"0\" max=\"1023\" min=\"0\" /></td>\n\r"
  "<td><div class=\"onoffswitch\"><input type=\"checkbox\" class=\"onoffswitch-checkbox\" id=\"cb13\" onclick=\"cb_check(this)\"/>\n\r"
  "<label class=\"onoffswitch-label\" for=\"cb13\"> <span class=\"onoffswitch-inner2\"></span> <span class=\"onoffswitch-switch\"></span></label>\n\r"
  "</div></td>\n\r"
  "</tr>\n\r";

const static char esp11[] PROGMEM =
  "<tr>\n\r"
  "<td>&nbsp;</td>\n\r"
  "<td class=\"column_view-m column_view-off\">&nbsp;</td>\n\r"
  "<td align=\"center\" class=\"mode_view-m mode_view-off\">r</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">&nbsp;</td>\n\r"
  "<td bgcolor=\"#E10000\">3.3v</td><td>&nbsp;</td>\n\r"
  "<td bgcolor=\"#666666\">&nbsp;</td>\n\r"
  "<td>GPIO15</td><td>D8</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\"><select id=\"sd15\" onchange=\"sel_test(this)\">\n\r"
  "<option style=\"background-color: #d9fffe\">Input</option><option style=\"background-color: #fffed9\" selected=\"selected\">Output</option></select></td>\n\r"
  "<td class=\"mode_view-m mode_view-off\"><input type=\"checkbox\" id=\"ci15\" onclick=\"cb_check0(this)\" /></td>\n\r"
  "<td align=\"center\" class=\"column_view-m column_view-off\"><input type=\"range\" onchange=\"polsun_val(this)\" class=\"rangeP\" id=\"cd15\" value=\"0\" max=\"1023\" min=\"0\" /></td>\n\r"
  "<td><div class=\"onoffswitch\">\n\r"
  "<input type=\"checkbox\" class=\"onoffswitch-checkbox\" id=\"cb15\" onclick=\"cb_check(this)\"/>\n\r"
  "<label class=\"onoffswitch-label\" for=\"cb15\">\n\r"
  "<span class=\"onoffswitch-inner2\"></span><span class=\"onoffswitch-switch\"></span></label>\n\r"
  "</div></td>\n\r"
  "</tr>\n\r";

const static char esp12[] PROGMEM =
  "<tr bgcolor=\"#CCCCCC\">\n\r"
  "<td>&nbsp;</td>\n\r"
  "<td class=\"column_view-m column_view-off\">system</td>\n\r"
  "<td align=\"center\" class=\"mode_view-m mode_view-off\">u</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">system</td>\n\r"
  "<td>EN</td>\n\r"
  "<td>EN</td>\n\r"
  "<td bgcolor=\"#666666\">&nbsp;</td>\n\r"
  "<td>GPIO3</td>\n\r"
  "<td>D9</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\"><strong>RXD0</strong></td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">&nbsp;</td>\n\r"
  "<td align=\"center\" class=\"column_view-m column_view-off\">&nbsp;</td>\n\r"
  "<td><div class=\"onoffswitch\"><input type=\"checkbox\" class=\"onoffswitch-checkbox\" id=\"cb3\" onclick=\"cb_check0(this)\"/>\n\r"
  "<label class=\"onoffswitch-label\" for=\"cb3\">\n\r"
  "<span class=\"onoffswitch-inner2\"></span> <span class=\"onoffswitch-switch\"></span></label>\n\r"
  "</div></td>\n\r"
  "</tr>\n\r"
  "<tr>\n\r"
  "<td>&nbsp;</td>\n\r"
  "<td class=\"column_view-m column_view-off\">system</td>\n\r"
  "<td align=\"center\" class=\"mode_view-m mode_view-off\">p</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">system</td>\n\r"
  "<td>RST</td><td>RST</td>\n\r"
  "<td bgcolor=\"#666666\">&nbsp;</td>\n\r"
  "<td>GPIO1</td>\n\r"
  "<td>D10</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\"><strong>TXD0</strong></td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">&nbsp;</td>\n\r"
  "<td align=\"center\" class=\"column_view-m column_view-off\">&nbsp;</td>\n\r"
  "<td><div class=\"onoffswitch\"><input type=\"checkbox\" class=\"onoffswitch-checkbox\" id=\"cb1\" onclick=\"cb_check0(this)\"/>\n\r"
  "<label class=\"onoffswitch-label\" for=\"cb1\">\n\r"
  "<span class=\"onoffswitch-inner2\"></span> <span class=\"onoffswitch-switch\"></span></label>\n\r"
  "</div></td></tr>\n\r";

const static char esp13[] PROGMEM =
  "<tr bgcolor=\"#E10000\"><td>&nbsp;</td><td class=\"column_view-m column_view-off\">&nbsp;</td>\n\r"
  "<td align=\"center\" class=\"mode_view-m mode_view-off\">t</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">&nbsp;</td><td  bgcolor=\"#FFFF9D\">Vin</td><td>&nbsp;</td><td bgcolor=\"#666666\">&nbsp;</td><td>&nbsp;</td><td>3.3v</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">&nbsp;</td>\n\r"
  "<td class=\"mode_view-m mode_view-off\">&nbsp;</td>\n\r"
  "<td align=\"center\" class=\"column_view-m column_view-off\">&nbsp;</td><td>&nbsp;</td></tr>\n\r"
  "</tbody></table><div id=\"ip\" title=\"#\"></div></body></html>\n\r";

const static char macro[] PROGMEM =
  "<div style=\"background-color:#006; width:100px\">\n\r"
  "<div class= \"ex_output\" style=\"width:168px\">\n\r"
  "<output id=\"ex_out\"><span>This is MACRO</span></output></div>\n\r"
  "<table id=\"pult\" title=\"macro\" width=\"176\" border=\"0\">\n\r"
  "<tr><td><div class=\"but\" id=but1 style=\"background-color:#0C6; color:#fff; width:168px\" onclick=\"sel_but(this)\"><span>Macro 1 - Утро&nbsp;</span></div></td></tr>\n\r"
  "<tr><td><div class=\"but\" id=but2 style=\"background-color:#2C526B; color: #fff; width:168px\" onclick=\"sel_but(this)\"><span>Кино</span></div></td></tr>\n\r"
  "<tr><td><div class=\"but\" id=but3 style=\"background-color: #E3CF1C; color:#fff; width:168px\" onclick=\"sel_but(this)\" ><span>Close home</span></div></td></tr>\n\r"
  "<tr><td><div class=\"but\" id=but4 style=\"background-color: #4885cb; color:#fff; width:168px\" onclick=\"sel_but(this)\" ><span>Open home</span></div></td></tr>\n\r"
  "<tr><td><div class=\"but\" id=but5 style=\"background-color: #AD5A88; color:#fff; width:168px\" onclick=\"sel_but(this)\" ><span>сегодня праздник</span></div></td></tr>\n\r"
  "<tr><td>&nbsp;</td></tr></table></div>\n\r"
  "<div id=\"ip\" title=\"#\"></div>\n\r"
  "</body></html>\n\r";

const static char socket[] PROGMEM =
  "<table width=\"265\" id=\"pult\" title=\"map\">\n\r"
  "<tr><td width=\"265\" colspan=\"2\"><div class= \"ex_output\">\n\r"
  "<output id=\"ex_out\"><span style=\"color:#CCC\">DevKit ESP8266 Control</span></output>\n\r"
  "<input type=\"checkbox\" id=\"te2\" checked/>\n\r"
  "</div></td></tr>\n\r"
  "<tr bgcolor=\"#E10000\">\n\r"
  "<td colspan=\"2\" align=\"center\"><span style=\"color:#FFF; font-weight:bold\">РОЗЕТКИ</span></td></tr>\n\r"
  "<tr><td colspan=\"2\">&nbsp;</td></tr>\n\r"
  "<tr><td><div class=\"css-checkbox\" style=\"padding-left:74px;\">\n\r"
  "<input type=\"checkbox\" id=\"vv12\" name=\"192.168.0.120\"  onclick=\"cb_check0(this)\"/>\n\r"
  "<label for=\"vv12\" >&bull;</label>\n\r"
  "</div></td>\n\r"
  "<td style=\"font-weight:bold\">.120</td></tr>\n\r"
  "<tr><td><div class=\"css-checkbox\" style=\"padding-left:74px;\">\n\r"
  "<input type=\"checkbox\" id=\"vv13\" name=\"192.168.0.130\"  onclick=\"cb_check0(this)\"/>\n\r"
  "<label for=\"vv13\" >&bull;</label>\n\r"
  "</div></td>\n\r"
  "<td style=\"font-weight:bold\">.130</td></tr>\n\r"
  "<tr><td colspan=\"2\"></td></tr>\n\r"
  "<tr bgcolor=\"#E10000\">\n\r"
  "<td colspan=\"2\">&nbsp;</td></tr>\n\r"
  "</table>\n\r"
  "<table width=\"265\" border=\"1\">\n\r"
  "<tr><th width=\"88\" scope=\"col\">таймер</th>\n\r"
  "<th width=\"87\" scope=\"col\"><input type=\"checkbox\" id=\"tt1\" onclick=\"my_timer()\" disabled=\"disabled\" />\n\r"
  "<input style=\"width:52px; text-align:center\" type=\"number\" min=\"1\" max=\"59\" step=\"1\" id=\"r_sec\" value=\"4\" onchange=\"timer_on()\" /></th>\n\r"
  "<th width=\"68\" scope=\"col\">sec</th></tr>\n\r"
  "</table>\n\r"
  "<div style=\"background-color:#C10000; height:4px; width:264px\" >&nbsp;</div>\n\r"
  "<div id=\"ip\" title=\"#\"></div>\n\r"
  "</body></html>\n\r";

const static char file_mp1[] PROGMEM =
  "<table id=\"pult\" title=\"mp3\" width=\"176\" border=\"1\" bgcolor=\"#000066\" bordercolor=\"#666666\" style=\"color:#888\">\n\r"
  "<tr><td colspan=\"3\"><div class= \"ex_output\" style=\"width:176px;\">\n\r"
  "<output id=\"ex_out\"><span>DevKit ESP8266 Control</span></output>\n\r"
  "</div></td></tr>\n\r"
  "<tr><td colspan=\"3\">&nbsp;</td></tr>\n\r"
  "<tr><td width=\"56\"><input type=\"radio\" name=\"mp3\" id=\"ff1\" value=\"ie\" onclick=\"sel_but(this)\">1</td>\n\r"
  "<td width=\"55\"><input type=\"radio\" name=\"mp3\" id=\"ff2\" value=\"ie\" onclick=\"sel_but(this)\">2</td>\n\r"
  "<td width=\"51\"><input type=\"radio\" name=\"mp3\" id=\"ff3\" value=\"ie\" onclick=\"sel_but(this)\">3</td></tr>\n\r"
  "<tr><td><input type=\"radio\" name=\"mp3\" id=\"ff4\" value=\"ie\" onclick=\"sel_but(this)\">4</td>\n\r"
  "<td><input type=\"radio\" name=\"mp3\" id=\"ff5\" value=\"ie\" onclick=\"sel_but(this)\">5</td>\n\r"
  "<td><input type=\"radio\" name=\"mp3\" id=\"ff6\" value=\"ie\" onclick=\"sel_but(this)\">6</td></tr>\n\r"
  "<tr><td><input type=\"radio\" name=\"mp3\" id=\"ff7\" value=\"ie\" onclick=\"sel_but(this)\">7</td>\n\r"
  "<td><input type=\"radio\" name=\"mp3\" id=\"ff8\" value=\"ie\" onclick=\"sel_but(this)\">8</td>\n\r"
  "<td><input type=\"radio\" name=\"mp3\" id=\"ff9\" value=\"ie\" onclick=\"sel_but(this)\">9</td></tr>\n\r"
  "<tr><td><input type=\"radio\" name=\"mp3\" id=\"ff10\" value=\"ie\" onclick=\"sel_but(this)\">10</td>\n\r"
  "<td><input type=\"radio\" name=\"mp3\" id=\"ff11\" value=\"ie\" onclick=\"sel_but(this)\">11</td>\n\r"
  "<td><input type=\"radio\" name=\"mp3\" id=\"ff12\" value=\"ie\" onclick=\"sel_but(this)\">12</td></tr>\n\r"
  "<tr><td><input type=\"radio\" name=\"mp3\" id=\"ff13\" value=\"ie\" onclick=\"sel_but(this)\">13</td>\n\r";


const static char file_mp2[] PROGMEM =
  "<td><input type=\"radio\" name=\"mp3\" id=\"ff14\" value=\"ie\" onclick=\"sel_but(this)\">14</td>\n\r"
  "<td><input type=\"radio\" name=\"mp3\" id=\"ff15\" value=\"ie\" onclick=\"sel_but(this)\">15</td></tr>\n\r"
  "<tr><td><input type=\"radio\" name=\"mp3\" id=\"ff16\" value=\"ie\" onclick=\"sel_but(this)\">16</td>\n\r"
  "<td><input type=\"radio\" name=\"mp3\" id=\"ff17\" value=\"ie\" onclick=\"sel_but(this)\">17</td>\n\r"
  "<td><input type=\"radio\" name=\"mp3\" id=\"ff18\" value=\"ie\" onclick=\"sel_but(this)\">18</td></tr>\n\r"
  "<tr><td><input type=\"radio\" name=\"mp3\" id=\"ff19\" value=\"ie\" onclick=\"sel_but(this)\">19</td>\n\r"
  "<td><input type=\"radio\" name=\"mp3\" id=\"ff20\" value=\"ie\" onclick=\"sel_but(this)\">20</td>\n\r"
  "<td><input type=\"radio\" name=\"mp3\" id=\"ff21\" value=\"ie\" onclick=\"sel_but(this)\">21</td></tr>\n\r"
  "<tr><td ><div class=\"key\" id=key_left onclick=\"sel_but(this)\"><span  style=\"font-size:24px; margin:2px;\">&laquo;</span></div></td>\n\r"
  "<td><div class=\"but\" id=ok onclick=\"sel_but(this)\"><span id=span_but style=\"font-size:24px;margin:2px;\">&#x25B6;</span></div></td>\n\r"
  "<td><div class=\"key\" id=key_right onclick=\"sel_but(this)\"><span  style=\"font-size:24px;margin:2px;\">&raquo;</span></div></td></tr>\n\r"
  "<tr><td colspan=\"3\" align=\"center\" >volume =<span >\n\r"
  "<output id=\"result1\">0</output></span></td></tr>\n\r"
  "<tr><td colspan=\"3\" align=\"center\"><input type=\"range\" onclick=\"polsun_val(this)\" class=\"rangeP\" id=\"cd30\" value=\"15\" max=\"30\" min=\"0\"  /></td></tr>\n\r"
  "</table><div class=\"cleared\"></div>\n\r"
  "<div class= \"ex_pult\" style=\"width:192px; margin-left:0px;\">\n\r"
  "<output id=\"pultname\"><span>mp3</span></output></div>\n\r"
  "<div id=\"ip\" title=\"192.168.0.123\"></div></body></html>\n\r";

const static char transponder1[] PROGMEM =
  "<div style=\"background-color: #5764D7; width:168px; margin-left:48px;\"><span style=\"color:#F00; font-weight:bold; font-size:36px; margin-left:60px\">IR</span>\n\r"
  "<table width=\"264px\" border=\"0\" cellspacing=\"0\" id=\"pult\" style=\" margin-left:-48px;\" title=\"transponder\">\n\r"
  "<tr><td style=\"color:#CCC; font-weight:bolder;\">&nbsp;</td>\n\r"
  "<td style=\"color:#CCC; font-weight:bolder; text-align:center;background-color: #000080\"><output id=\"ex_time\"><span>DevKit ESP8266</span></output></td>\n\r"
  "<td style=\"color:#CCC; font-weight:bolder;\">&nbsp;</td></tr>\n\r"
  "<tr><td>&nbsp;</td>\n\r"
  "<td style=\"color:#CCC; font-weight:bolder; text-align:center;background-color: #000080\"><output id=\"ex_out\"><span>IR transponder</span></output></td>\n\r"
  "<td>&nbsp;</td></tr>\n\r"
  "<tr style=\"font-size:8px\"><td>&nbsp;</td><td>&nbsp;</td><td>&nbsp;</td></tr>\n\r"
  "<tr><td>&nbsp;</td><td><div class=\"css-checkbox\" style=\"padding-left:60px;\">\n\r"
  "<input type=\"checkbox\" id=\"ir0\"  onclick=\"cb_check0(this)\"/>\n\r"
  "<label for=\"ir0\" >&bull;</label></div></td>\n\r"
  "<td>&nbsp;</td></tr>\n\r"
  "<tr><td style=\"color:#CCC; font-weight:bolder\">&nbsp;</td>\n\r"
  "<td style=\"color:#CCC; font-weight:bolder\"><span style=\"margin-left:32px;\">куда транслировать</span></td>\n\r"
  "<td style=\"color:#CCC; font-weight:bolder\">&nbsp;</td></tr>\n\r";

const static char transponder2[] PROGMEM =
  "<tr bgcolor=\"#000099\"><td><span class=\"ex_output\">\n\r"
  "<input type=\"radio\" name=\"trad\" id=\"r1\" onclick=\"r_check(this)\" style=\"padding-left:-30px; position:static\" /></span></td>\n\r"
  "<td style=\"color:#CCC; font-weight:bolder; background-color: #000080\"><output id=\"ex_r1\"><span>192.168.0.130; - спальня</span></output></td>\n\r"
  "<td><span class=\"ex_output\"><input type=\"checkbox\" id=\"ir1\" checked onclick=\"cb_check0(this)\"/></span></td></tr>\n\r"
  "<tr style=\"font-size:2px\"><td>&nbsp;</td><td>&nbsp;</td><td>&nbsp;</td></tr>\n\r"
  "<tr bgcolor=\"#000099\"><td><span class=\"ex_output\">\n\r"
  "<input type=\"radio\" name=\"trad\" id=\"r2\" onclick=\"r_check(this)\" /></span></td>\n\r"
  "<td style=\"color:#CCC; font-weight:bolder; background-color: #000080\"><output id=\"ex_r2\"><span>192.168.0.131; - кухня</span></output></td>\n\r"
  "<td><span class=\"ex_output\"><input type=\"checkbox\" id=\"ir2\" checked onclick=\"cb_check0(this)\"/></span></td></tr>\n\r"
  "<tr style=\"font-size:2px\"><td>&nbsp;</td><td>&nbsp;</td><td>&nbsp;</td></tr>\n\r"
  "<tr bgcolor=\"#000099\"><td><span class=\"ex_output\">\n\r"
  "<input type=\"radio\" name=\"trad\" id=\"r3\" onclick=\"r_check(this)\" /></span></td>\n\r"
  "<td style=\"color:#CCC; font-weight:bolder; background-color: #000080\"><output id=\"ex_r3\"><span>192.168.0.132; - зал</span></output></td>\n\r"
  "<td><span class=\"ex_output\"><input type=\"checkbox\" id=\"ir3\" checked onclick=\"cb_check0(this)\"/></span></td></tr>\n\r";

const static char transponder3[] PROGMEM =
  "<tr style=\"font-size:2px\"><td>&nbsp;</td><td>&nbsp;</td><td>&nbsp;</td></tr>\n\r"
  "<tr bgcolor=\"#000099\"><td><span class=\"ex_output\"><input type=\"radio\" name=\"trad\" id=\"r4\" onclick=\"r_check(this)\" /></span></td>\n\r"
  "<td style=\"color:#CCC; font-weight:bolder; background-color: #000080\"><output id=\"ex_r4\"><span>192.168.0.133; - детская</span></output></td>\n\r"
  "<td><span class=\"ex_output\">\n\r"
  "<input type=\"checkbox\" id=\"ir4\" checked onclick=\"cb_check0(this)\"/></span></td></tr>\n\r"
  "<tr><td>&nbsp;</td><td>&nbsp;</td><td>&nbsp;</td></tr>\n\r"
  "<tr><td>&nbsp;</td>\n\r"
  "<td><div><input type=\"text\" id=\"ex_input\" style=\"margin-left:16px; width:160px; text-align:center\"></div></td>\n\r"
  "<td>&nbsp;</td></tr>\n\r"
  "<tr><td align=\"center\">&nbsp;</td>\n\r"
  "<td align=\"center\"><div class=\"but\" id=\"but_rec\" style=\"background-color:  #999; color: #F00; height:22px; width:120px;\" onclick=\"rec_edit(this);\" >\n\r"
  "<span style=\"margin-top:3px\">записать</span> </div></td>\n\r"
  "<td align=\"center\">&nbsp;</td></tr>\n\r"
  "<tr><td>&nbsp;</td><td align=\"center\"><span style=\"color:#CCC; font-weight:bolder\">опрос</span></td><td>&nbsp;</td></tr>\n\r"
  "<tr><td style=\"border-width:2px;   border-style:solid\">&nbsp;<input id=\"zz=0\" type=\"button\" onClick=\"sel_but(this)\" />&nbsp;</td>\n\r"
  "<td align=\"center\" style=\"color:#CCC; font-weight:bolder; border-width:2px; border-style:solid\">каждые\n\r"
  "<input type=\"number\" min=\"1\" max=\"59\" step=\"1\" id=\"r_sec\" style=\"width:72; text-align:center\" value=\"30\"/>\n\r"
  "<span style=\"color:#006\">&nbsp;сек.</span></td>\n\r"
  "<td style=\"border-width:2px;   border-style:solid\"><input type=\"checkbox\" id=\"tt1\" onclick=\"my_timer(this)\" /></td></tr>\n\r"
  "<tr><td>&nbsp;</td><td><span id=\"my_clock\" style=\"color: #eee;  font-weight: bold; margin-left:96px;\">00:10</span></td><td>&nbsp;</td></tr>\n\r"
  "</table></div><div id=\"ip\" title=\"#\"></div></body></html>\n\r";

const char* const html_esp[] PROGMEM = {esp0, esp1, esp2, esp3, esp4, esp5, esp6, esp7, esp8, esp9,
                                        esp10, esp11, esp12, esp13
                                       };
const char* const html_header[] PROGMEM = {header0, header1, header2, header3, header4, header5, header6, header7,
                                           header8, header9, header10, header11, header12, header13, header14
                                          };
//const char* const html_daily[] PROGMEM = {daily1, daily2};
const char* const html_samsung[] PROGMEM = {samsung1, samsung2, samsung3};
const char* const html_light1[] PROGMEM = {light1_1, light1_2, light1_3};
const char* const html_light2[] PROGMEM = {light2_0, light2_1, light2_2};
const char* const html_map[] PROGMEM = {map1, map2, map3};
const char* const html_mp3[] PROGMEM = {file_mp1, file_mp2};
const char* const html_transponder[] PROGMEM = {transponder1, transponder2, transponder3};

const char* ssid = "******";                   //имя WiFi сети (WiFi router)
const char* password = "************";   //ключ или пароль WiFi сети (WiFi router)

String mess = "";
String mess_wifi = "";
String  priem = "";
int pin = 2;
int sos = 0;
int Key_avto = 0;            //ключ передачи данных по таймеру
int Key_output = 1;          //1- автоматически устанавливать mode = OUTPUT
int cikl_current = 1;        //интервал ~1 сек
int cikl_avto = 1000;        //кол-во циклов процессора для задержки второго послания данных

String pult_name = "no_name";
String key_name = "but0";
boolean stringComplete = false;        //флаг поступления COM кода
String inputString = "";               //для COM


const int max_len = 76; //максимальная длина IR кода в байтах

unsigned int irSignal[max_len];
String cod_note = "";
String cod_hex = "12345678";
int cod_bits = 32;  //кол-во бит
int cod_khz = 43;  //частота
int cod_len;           //кол-во байт в коде
int ind1;
int ind2;
String pult_mode = "IR";  //"RF";
boolean fl_rec = false;
int count_timer = 0;
boolean fl_macro = false;
String macro_str;
//IPAddress ip(192, 168, 0, 177);
String Get_ip;
//===========new ex===============
// если делать универсальный анализ
String ex_link = "";
String ex_ip = "";
//========================
String out_map = "ex_out;";
String out_rozetka = "out1;";
String out_daily = "ex_r1;ex_r2;ex_r3;ex_r4;ex_time;";
String par_daily;
#define DS1302_GND_PIN 33
#define DS1302_VCC_PIN 35
DS1302RTC RTC(16, 4, 5);  //
boolean fl_day_open = false;
int r_h, r_min, r_sec, r_y, r_m, r_d, r_wd, r_d_current;
int r_time;
String r_data;
//========================================
String DD1;
String DD2;
String DD3; //     задание на день!!!
String ex_DD1;
String ex_DD2;
String ex_DD3;
String ex_DD4;
//=========для ретранслятора===============================
//String ex_TD[5] = {"", "192.168.0.130; зал", "192.168.0.131; спальня", "192.168.0.132; кухня", "192.168.0.133; ESP8266"};
String ex_TD[5] = {"", "", "", "", ""};
boolean cb_TD[5] = {1, 1, 0, 0, 0}; //[0] - это выключатель
//=========================================================
WiFiServer server(80);
WiFiClient client = server.available();
//--
IPAddress my_ip(192, 168, 0, 128); //При изменении IP найди в header[] этот адрес и также измени
IPAddress gateway(192, 168, 0, 1);
IPAddress subnet(255, 255, 255, 0); //- See more at: http://www.esp8266.com/viewtopic.php?f=6&t=3522#sthash.sWvIJ5y5.dpuf
byte ip[] = { 192, 168, 0, 121 };  //2016-1

void setup()
{
  initHardware();                 //инициализация железа в одном месте
  //http://uc.org.ru/node/86
  Serial.printf("\n\nFree memory %d\n", ESP.getFreeHeap());
  irrecv.enableIRIn();          // Start the receiver

  //cod_hex.reserve(11);     //для восьми символов + \n\r
  //=== Connect to WiFi network===
  //   WiFi.config(ip);
  //status = WiFi.begin(ssid, pass);
  WiFi.begin(ssid, password);
  WiFi.config(my_ip, gateway, subnet);
  Serial.println();
  // Wait for connection
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("R_mDNS_Web_Server_09");
  Serial.print("Connected to ");
  Serial.println(ssid);
  Serial.print("IP address: ");
  Serial.println(WiFi.localIP());
  //  Serial.println(WiFi.localIP());
  IPAddress my_ip = WiFi.localIP();

  Get_ip = "?ip=" + String(my_ip[0]) + '.' + String(my_ip[1]) + '.' + String(my_ip[2]) + '.' + String(my_ip[3]);
  //  Get_ip = "{go_link = \"http://\"+document.getElementById(\"ip\").title + \"/?ip=" +
  //           String(ip[0]) + '.' + String(ip[1]) + '.' + String(ip[2]) + '.' + String(ip[3]) + ";\";\n\r";
  if (!MDNS.begin("esp8266")) {
    Serial.println("Error setting up MDNS responder!");
    while (1) {
      delay(1000);
    }
  }
  Serial.println("mDNS responder started");
  // Start TCP (HTTP) server
  server.begin();
  Serial.println("TCP server started");
  // Add service to MDNS-SD
  MDNS.addService("http", "tcp", 80);
  //составить расписание на день
}

//&#11h30m00s::/map?ip=192.168.0.120;map:vv12=1
void time_razbor(String str_time) {
  ind1 = str_time.indexOf("&#");
  ind2 = str_time.indexOf('h');
  r_h = str_time.substring(ind1 + 2, ind2).toInt(); //inputString.length());
  str_time.remove(0, ind2 + 1); //второй параметр указывает на кол-во символов!!!
  ind2 = str_time.indexOf('m');
  r_min = str_time.substring(0, ind2).toInt();
  str_time.remove(0, ind2 + 1);
  ind2 = str_time.indexOf('s');
  r_sec = str_time.substring(0, ind2).toInt();
  str_time.remove(0, ind2 + 3);
  //Serial.println("str_time= " + str_time);
}

int digit_time(String d_time) {
  int result;
  String buf_str;
  time_razbor(d_time);
  buf_str = int2digit(String(r_h)) + int2digit(String(r_min)) + int2digit(String(r_sec));
  //Serial.println("buf_str" + buf_str);
  result = buf_str.toInt();
  //Serial.println(result,DEC);     //переполняется стек
  return result;
}

String int2digit(String dig2) {
  String result = dig2;
  if (dig2.toInt() < 10) { //dig2.length()<
    result = "0" + dig2;
  }
  return result;
}

void day_job(int dg_time, String ins_ready) {
  String Buf_0 = DD1;
  String str_temp;
  DD1 = "";
  fl_rec = false;
  while (Buf_0.indexOf("&#") >= 0) { //проссмотреть  в цикле весь созданный ранее массив
    ind1 =  Buf_0.indexOf("&#");
    ind2 =  Buf_0.indexOf("::");
    str_temp = Buf_0.substring(ind1, ind2 + 2);   //выделить время
    r_time = digit_time(str_temp);                //получить код времени
    if  (dg_time <= r_time && fl_rec == false) {  //новая строка имеет более раннее время
      DD1 = DD1 + ins_ready;                      //сначала вставим новую строку
      fl_rec = true;
    }
    Buf_0.remove(0, ind1 + 2);              //удалили ключ начала строки
    ind1 = Buf_0.indexOf("&#");             //начало следующей строки
    if (ind1 >= 0) {
      str_temp = Buf_0.substring(0, ind1);
      Buf_0.remove(0, ind1);                //удалить все до начала строки
      //=========================
      str_temp = "&#" + str_temp;           //строка для вставки из готового массива
      //=========================
    } else {
      str_temp =  "&#" + Buf_0;
    }
    DD1 = DD1 + str_temp;                        //вставляем строку из готового массива
  }                                              //цикл завершен
  if (fl_rec == false) {                         //записи не был о - строка по времени позже всех
    DD1 = DD1 + ins_ready;                       //вставить новую строку в конец
  }
  fl_rec = false;
}

String select_str(String DD0) { //
  String result = "";
  return result;
}

void form_dd1(String DD0) {
  String str_temp = "";
  while (DD0.indexOf("&#") >= 0 ) {
    ind1 = DD0.indexOf("&#");                   //найти начало строки
    if (ind1 >= 0 ) {
      ind2 = DD0.indexOf("::");                 //конец записи времени
      str_temp = DD0.substring(ind1, ind2 + 2); //выделили начало с записью времени для ПОИСКА
      //=========================
      r_time = digit_time(str_temp);        //получил код времени
      //=========================
      DD0.remove(0, ind1 + 2);  //удалили ключ начала строки
      ind1 = DD0.indexOf("&#"); //начало следующей строки
      if (ind1 >= 0) {          //есть еще строка
        str_temp = DD0.substring(0, ind1); //
        DD0.remove(0, ind1);               //удалить все до начала следующей строки
        //=========================
        str_temp = "&#" + str_temp;        //строка для вставки
        //=========================
      } else {
        str_temp =  "&#" + DD0;            //последняя строка
      }
      //======================
      day_job(r_time, str_temp);           //вставка в цикле строк в DD1;
      //======================
    }
  }
}

void read_daily(void) {
  //читает следующую строку расписания
  ind1 = DD1.indexOf("&#");
  if (ind1 >= 0) {
    DD1.remove(0, ind1 + 2);    //удалить ключ начала строки
    ind1 = DD1.indexOf("&#");   //есть еще строка?
    if (ind1 >= 0) {
      DD2 = DD1.substring(0, ind1);  //строка готова
      DD1.remove(0, ind1);           //удалить строку
      DD2 = "&#" + DD2;              //восстановили формат
    } else {                                 //последняя строка
      DD2 =  "&#" + DD1;
      DD1 = "";
    }
    ind2 = DD2.indexOf("::"); //конец записи времени
    cod_note = DD2.substring(0, ind2 + 2); //выделили начало с записью времени
    //=========================
    //    r_time = digit_time(cod_note);        //получил код времени
    //    Serial.println("r_time " + String(r_time));
  }
}

void fifo_list(void) {
  ex_DD1 = ex_DD2;
  ex_DD2 = ex_DD3;
  ex_DD3 = ex_DD4;
  if (DD1 != "") {
    read_daily();          //следующее задание в готовность из списка DD1:cod_note - начало с записью времени
    ex_DD4 = DD2;            //добавить новую строку расписаеия
  } else {
    ex_DD4 = "---end---";  //расписание закончилось
  }
  DD2 = ex_DD1;            //следующую команду на исполенение в DD2!!!
  //-=====-
  ind2 = DD2.indexOf("::");                //конец записи времени
  if (ind2 >= 0) {                         //в DD2 есть код времени
    cod_note = DD2.substring(0, ind2 + 2); //выделили начало с записью времени
    //=========================
    r_time = digit_time(cod_note);         //получил код времени ближайшей команды
    //-====-
  }
  par_daily = param_mess("daily", out_daily, '%');
}

void loop(void) {
  /*
        if(millis()%1000==0){ // если прошла 1 секунда
        Serial.println(time.gettime("d-m-Y, H:i:s, D")); // выводим время
        delay(1); // приостанавливаем на 1 мс, чтоб не выводить время несколько раз за 1мс
      }
      */
  if (count_timer > 0 ) {  //есть в очереди макро команда
    count_timer -= 1;       //примерно секунда, зависит от задержки в конце цикла loop
    if (count_timer <= 0 ) {    //счет закончен - исполнить следующую
      razdel_macro();            //вычленить следующую команду
    }
  }
  serialEvent(); // проверка поступления кодов
  if (stringComplete) {
    Serial.println("COM");
    Serial.println(inputString);
    ind2 = inputString.indexOf("pult=");            //указатель имени пульта
    if (ind2 >= 0) {  //это имя
      ind1 = inputString.indexOf("=");            //указатель имени пульта
      pult_name = inputString.substring(ind1 + 1, inputString.length());
      Serial.print("pult_name ");
      Serial.println(pult_name);
    } else {                        //если это не имя пульта, значит это имя кнопки или примечание
      ind2 = inputString.indexOf("/");        //разделитель примечания
      if (ind2 > 0 ) {    //есть имя
        key_name = inputString.substring(0, ind2);
      }
      if (ind2 >= 0 ) {  //может быть примечание
        cod_note = inputString.substring(ind2 + 1, inputString.length());
      }
    }
    inputString = "";
    stringComplete = false;
  }

  //*******************часы********************
  tmElements_t tm;
  if (! RTC.read(tm)) {
    r_h = tm.Hour;
    r_min = tm.Minute;
    r_sec = tm.Second;
    r_y = tm.Year + 1970;
    r_m = tm.Month;
    r_d = tm.Day;
    r_wd = tm.Wday;  //день недели
  }
  r_data = String(r_h) + ":" + String(r_min) + ":" + String(r_sec) + "    " + String(r_d) + "/" + String(r_m) + "/" + String(r_y);
  //time_razbor("&#11h30m13s::/map?ip=192.168.0.120;map:vv12=1");
  if (r_d_current != r_d) {  //смена дня
    fl_day_open = false;
    r_d_current = r_d;
  }

  if (fl_day_open == false) {
    DD1 = time_macro("R_TIME", "DAILY.TXT");
    //      Serial.println("DAILY - " + DD1);
    DD2 = time_macro("R_TIME", "Day" + String(tm.Wday) + ".TXT"); //день недели
    //Serial.print(tm.Wday);
    //Serial.println("D2 - " + DD2);

    DD3 = String(tm.Month) + "_" + String(tm.Day) + ".TXT";
    DD3 = time_macro("R_TIME/" + String(tm.Year + 1970), DD3);
    //переменные времени больше не нужны и можно их использовать
    //===формирование расписания на день
    //исходный DD1. Построчно проверяем DD2, а затем и DD3, чтобы вписать в нужное место в DD1
    //нужна строка (для вставки), код времени (для сравнения)
    form_dd1(DD2);
    form_dd1(DD3);
    //===========================
    //Расписание дня готово - DD1
    Serial.println("DD1 " + DD1);
    //===========================
    DD2 = "";      DD3 = "";
    fl_day_open = true;
    read_daily();  //первое задание в готовность 4 раза
    delay(100);
    ex_DD1 = DD2;
    read_daily();  //первое задание в готовность 4 раза
    delay(100);
    ex_DD2 = DD2;
    read_daily();  //первое задание в готовность 4 раза
    delay(100);
    ex_DD3 = DD2;
    read_daily();  //первое задание в готовность 4 раза
    delay(100);
    ex_DD4 = DD2;
    fifo_list();
  }
  //******   **********часы*******   **********

  //часы
  r_h = digit_time("&#" + String(tm.Hour) + "h" + String(tm.Minute) + "m" + String(tm.Second) + "s");
  //  Serial.println("r_h "+r_h);
  //  Serial.println("r_time "+r_time);

  if (r_h >= r_time) {         //время выполнения команды
    if ((r_h - r_time) < 500) { //можно выполнять
      ind2 = DD2.indexOf("::");
      DD2 = "&#" + DD2.substring(ind2 + 2, DD2.length());
      macro_str = DD2;
      razdel_macro();
      fifo_list();
      //      read_daily();  //следующее задание в готовность
    } else { //время ушло
      fifo_list();
      //read_daily();  //следующее задание в готовность
    }
  }

  // Check if a client has connected
  client = server.available();
  if (client == true)
  {
    // Wait for data from client to become available
    while (client.connected() && !client.available()) {
      delay(10);
    }
    // Read the first line of HTTP request
    String req = client.readStringUntil('\r');
    //    String req = client.readString();
    priem = req;                    //ruben
    mess = "";                      //ruben
    analis();                       //ruben
    // return;
  }
  //==
  if (irrecv.decode(&results)) { //принята IR команда
    if (fl_rec == true || cb_TD[0] == true) { //разрешена запись или трансляция ИК команд
      cod_len = results.rawlen;
      //проверка параметров
      Serial.print("type  ");
      Serial.print(results.decode_type);
      Serial.print("  ");
      Serial.print(" (");
      Serial.print(results.bits, DEC);
      Serial.print(" bits)  ");
      Serial.print("len "); Serial.println(cod_len);
      Serial.print("button: "); Serial.print(key_name);  Serial.print("; cod(HEX): "); Serial.println(results.value, HEX);
      //cod_note;
      cod_hex = String(results.value, HEX); //запись кода кнопки = имя строки
      cod_bits = results.bits; //запись кода кнопки = имя строки
      //cod_fr = 38; //частота
      //cod_len = results.rawlen;  //codeLen
      //cod_ir[0] = "!!!" + String(results.value,HEX)+":::"; //запись кода кнопки = имя строки
      irrecv.resume(); // Receive the next value

      if (fl_rec == false) {                                       //разрешена только трансляция)
        //****************************************************************************************
        //irrecv.resume(); // Receive the next value
        //Serial.println("save!!!");

        //обнуляем ВЕСЬ буфер - только так я добился однозначных результатов!!!
        //      for (int i = 0; i <= sizeof(irSignal) / sizeof(irSignal[0]); i++) {
        //for (int i = 0; i <= 75; i++) {
        for (int i = 0; i < max_len; i++) {            //обнуление
          irSignal[i] = (char)0;
        }
        //формируем строку кода
        //      for (int i = 0; i <= cod_len; i++) {
        for (int i = 1; i <= cod_len; i++) {
          irSignal[i - 1] += (char)results.rawbuf[i];
        }
        //====================
        send_ircod();    //===
        //====================
        //       send_irwifi();   //===
        //====================
        //****************************************************************************************

      } else {                                 //разрешена запись - приоритет - в это время не может быть трансляции
        //if (fl_rec == true) {
        //####### запись на SD #######
        File_patch = pult_mode + "/" + pult_name;
        SD.mkdir((char *)File_patch.c_str());
        File_patch = (char *)File_patch.c_str();
        File_name =  File_patch + "/" + pult_name + ".txt";
        Serial.println(File_name);
        //=======================
        //if_exists();  //Если такой файл есть, то удалить (File_name)
        //=======================
        myFile = SD.open(File_name, FILE_WRITE);   //создать файл
        if (myFile) {
          //myFile.print("0x");
          myFile.print(key_name);         //1 - первый символ, это название клавиши=имя файла
          myFile.print("=");              //1=
          myFile.print(String(cod_hex));  //1=EEAA7755
          myFile.print(";");              //1=EEAA7755;
          myFile.print(String(cod_bits)); //1=EEAA7755;32   кол-во бит
          myFile.print(";");              //1=EEAA7755;32;
          myFile.print(String(cod_khz));  //1=EEAA7755;32;38   частота
          myFile.print(";");              //1=EEAA7755;32;38;
          myFile.print(String(cod_len));  //1=EEAA7755;32;38;62
          myFile.print("/");              //1=EEAA7755;32;38;62/
          myFile.print(cod_note);         //1=EEAA7755;32;38;62/примечание
          myFile.println();
          myFile.close();                 //закрыть файл
        }
        //===============================
        File_name =  File_patch + "/" + key_name + ".raw";
        //File_name =  File_patch + "/" + cod_hex + ".raw";
        //=======================
        if_exists(); //File_name
        //=======================
        myFile = SD.open(File_name, FILE_WRITE); //создать файл
        if (myFile) {
          for (int i = 1; i <= cod_len; i++) {
            myFile.write((char)results.rawbuf[i]); //запись байта
          }
          myFile.println();

          myFile.close();  //закрыть файл
        }
        /*
        //test start=============================
        for (int i2 = 1; i2 <= cod_len; i2++) {
          Serial.print(results.rawbuf[i2]); Serial.print(" ");
          delay(20);
        }
        //test end===============================
        */
        //========= получение последовательности по методу IRremote.h
        delay(1000);
        Serial.println("====IR command is ready====");
        //com_IR(); //можно проверить
      }
      delay(600);       //Время скважности для проверки записанной команды
    }
  }
  //Serial.print("."); //для проверки в режиме тестирования
}

void if_exists() {
  if (SD.exists((char *)File_name.c_str()) == true) {
    Serial.println("-=file removed=-");
    SD.remove((char *)File_name.c_str()); //удалить старый файл
  }
}

void analis()
{
  Serial.println("input  " + priem);
  priem = decode_(priem);
  //Serial.println("input 2 " + priem);
  //===========new ex===============
  // если делать универсальный анализ
  ex_ip = "";
  ex_link = "";
  ind1 = priem.indexOf("?ip=");              //это внешний запрос?
  if (ind1 >= 0) {                               //да  Get /?ip=192.168.0.121;map:cb12=0 HTTP/1.1
    ind2 = priem.indexOf(';');
    ex_ip = priem.substring(ind1 + 4, ind2);      //ip запроса
    priem.remove(0, ind2 + 1);             //удаляем все вплоть до ";"
    ind1 = priem.indexOf(':');
    ex_link = priem.substring(0, ind1);
    priem.remove(0, ind1 + 1);            //cb12=0 HTTP/1.1
    ind1 = priem.indexOf("HTTP/1.1");
    if (ind1 >= 0) {
      priem.remove(ind1, priem.length()); //cb12=0
    }
    convert_ip(ex_ip);   //получаем четыре байта ip[] = {192, 168, 0, 121};
    if (client.connect(ip, 80)) {
      //Serial.println("connected");
      //ok      client.println("GET /search?q=arduino HTTP/1.0");
      client.println( Get_ip + ";" + ex_link + ":" + priem);
      client.println();
    } else {
      Serial.println("connection failed");
    }
    return;
  }
  //=====end===new ex===============
  ind1 = priem.indexOf('?');              //признак передачи параметров
  if (ind1 < 0) {                         //смена html пульта
    ind1 = priem.indexOf("HTTP");         //пульты или МАКРО
    if (ind1 <= 7) {
      page_index();                       //основная страница, например, samsung
    } else {                              //вызов другой страницы html
      client.flush();
      //ruben      mess = "HTTP / 1.1 200 OK\r\n";
      //ruben      mess += "Content - Type: text / html\r\n\r\n";
      //client.print(mess);                 //подтверждение: вызов принят
      for (int i = 0; i < 15; i++)       //грузим общий HEADER
      { mess = html_header[i];
        client.print(mess);
        delay( 10 );
      }
      //найти и загрузить остальную часть html страницы GET /jvc HTTP/1.1
      ind1 = priem.indexOf("/");         // GET /jvc HTTP/1.1
      priem.remove(0, ind1 + 1);           // jvc HTTP/1.1
      ind1 = priem.indexOf(" HTTP");
      priem.remove(ind1, priem.length()); // jvc
      Serial.println("+change+");
      Serial.println(priem);
      //============выделили имя пульта=====================
      if (priem == "sony" || priem == "panasonic" || priem == "jvc" || priem == "horizont" || priem == "lg" || priem == "philips" || priem == "samsung") {
        for (int i = 0; i < 3; i++) //индекс указателя массива всегда с НУЛЯ!!!
        { mess = html_samsung[i];
          client.print(mess);
          delay( 10 );
        }
      }
      else {
        ind1 = priem.indexOf("ESP8266");  //управление ESP8266
        if (ind1 >= 0) {
          control_wifi();
        } else {
          //-=========================================================
          ind1 = priem.indexOf("light1"); //IR пульт светодиодной ленты
          if (ind1 >= 0) {
            for (int i = 0; i < 3; i++) //индекс указателя массива всегда с НУЛЯ!!!
            { mess =  html_light1[i];
              client.print(mess);
              pult_name = "light1";
              delay( 10 );
            }
          } else {
            ind1 = priem.indexOf("light2");
            if (ind1 >= 0) {              //IR пульт второй светодиодной ленты
              for (int i = 0; i < 3; i++) //индекс указателя массива всегда с НУЛЯ!!!
              { mess =  html_light2[i];
                client.print(mess);
                pult_name = "light2";
                delay( 10 );
              }
            } else {
              ind1 = priem.indexOf("grail");
              if (ind1 >= 0) {              //IR пульт штор (электро карниз)
                mess = grail0;
                client.print(mess);
                pult_name = "grail";
                delay( 10 );
              }
              else {
                ind1 = priem.indexOf("socket");
                if (ind1 >= 0) {              //IR пульт штор (электро карниз)
                  mess = socket;
                  client.print(mess);
                  pult_name = "socket";
                  delay( 10 );
                }
                else {
                  ind1 = priem.indexOf("map");
                  if (ind1 >= 0) {
                    for (int i = 0; i < 3; i++) //индекс указателя массива всегда с НУЛЯ!!!
                    { mess =  html_map[i];
                      client.print(mess);
                      pult_name = "map";
                      delay( 10 );
                    }
                  } else {
                    ind1 = priem.indexOf("macro"); //открыть макро пульт это макрокоманда
                    if (ind1 >= 0) {
                      mess =  macro;
                      client.print(mess);
                      pult_name = "macro";
                      delay( 10 );
                    } else {
                      ind1 = priem.indexOf("daily"); //открыть календарь
                      if (ind1 >= 0) {
                        mess =  daily1;
                        client.print(mess);
                        delay( 100 );
                        mess =  daily2;
                        client.print(mess);
                        pult_name = "daily";
                        /*

                         for (int i = 0; i < 2; i++) //индекс указателя массива всегда с НУЛЯ!!!
                          mess =  html_daily[i];
                        client.print(mess);
                        pult_name = "daily";
                        delay( 10 );
                        */
                      } else {
                        ind1 = priem.indexOf("mp3"); //открыть календарь
                        if (ind1 >= 0) {
                          for (int i = 0; i < 2; i++)
                          { mess =  html_mp3[i];
                            client.print(mess);
                            delay( 10 );
                          }
                          pult_name = "mp3";
                        } else {
                          ind1 = priem.indexOf("transponder"); //
                          if (ind1 >= 0) {
                            for (int i = 0; i < 3; i++)
                            { mess =  html_transponder[i];
                              client.print(mess);
                              delay( 10 );
                            }
                            pult_name = "transponder";

                          }
                        }
                      }
                    }
                  }
                }
              }
            }
          }
        }
      }
    }
  } else {                            //если есть ?
    ind2 = priem.indexOf("result=");  //нам не интересно имя пульта
    if (ind2 >= 0) {                  //это ответ от другого модуля
      ex_analis(priem);                    //анализ строки
      /*
      priem.remove(0, ind2);          //убрали начало запроса
      priem = "Get " + priem;
      Serial.println("result mess "+ priem);
      out_mess(priem);               //просто переслать? или анализ в ex-mess();
      */
    } else {
      //Serial.println("init_mess1");
      init_mess();                   //выполнение команд
    }
  }
}

String  modul_name, par_rozetka, par_map;

void ex_analis(String ex_priem)
{ //  /map?result=&16=0&5=0&4=1023&0=0&12=1023&13=1023
  //Serial.println("ex***= " + ex_priem);
  ind1 = ex_priem.indexOf('?');
  modul_name  =  ex_priem.substring(1, ind1);   //map
  ind1 = ex_priem.indexOf("result=");
  if (ind1 >= 0) {
    ex_priem.remove(0, ind1);     //result=&16=0&5=0&4=1023&0=0&12=1023&13=1023
    ind1 = ex_priem.indexOf("=");
    ex_priem.remove(0, ind1 + 1);   //&16=0&5=0&4=1023&0=0&12=1023&13=1023
    if (modul_name == "map") {
      //par_map = ex_priem;
      par_map = param_mess(ex_priem, out_map, ';');
      //    Serial.println("par_map= "+par_map);
    } else {
      if (modul_name == "rozetka") {
        //par_rozetka = ex_priem;
        par_rozetka = param_mess(ex_priem, out_rozetka, ';');
      }
    }
  }
}

boolean change_fl_rec(String m_str) {
  boolean result = false;
  ind2 = m_str.indexOf(":");  //нам не интересно имя пульта
  m_str.remove(0, ind2 + 1);
  ind2 = m_str.indexOf("=");  //нам не интересно имя пульта
  String str_temp = m_str.substring(0, ind2);
  if (str_temp == "rec") {
    result = true;
    if (mess.indexOf("=1") >= 0) {
      fl_rec = true;
      irrecv.resume(); // обнулить буфер IR декодера
    } else {
      fl_rec = false;
    }
  }
  return result;
}


void read_macro() { //вся информация в строке priem
  macro_str = "";
  ind1 = mess.indexOf(':'); //формат макро macro:name_file   /MACRO
  File_patch = "MACRO/";
  File_patch = (char *)File_patch.c_str();
  File_name =  File_patch + mess.substring(ind1 + 1, priem.length()) + ".txt";  //выделили номер кнопки
  Serial.println("MACRO - " + File_name);
  if (SD.exists((char *)File_name.c_str()) == true) {  //есть такой файл
    //  if (SD.exists((char *)File_name.c_str()) == true) {  //есть такой файл
    Serial.println("MACRO2 - " + File_name);
    myFile = SD.open(File_name);                       //открыть  файл
    if (myFile) {
      while (myFile.available()) {
        macro_str +=  char(myFile.read());
      }
      Serial.println("razdel_macro");
      razdel_macro();  //извлечь первую команду на исполненние
      Serial.println("MACRO_com - " + macro_str);
      // ==close the file:==
      myFile.close();  //закрыть файл
    }
  }
}

String time_macro(String folder_, String f_name) { //вся информация в строке mess
  String result = "";
  File_patch = folder_ + "/";
  File_patch = (char *)File_patch.c_str();
  File_name =  File_patch + f_name;
  if (SD.exists((char *)File_name.c_str()) == true) {  //есть такой файл
    myFile = SD.open(File_name);                       //открыть  файл
    if (myFile) {
      while (myFile.available()) {
        result +=  char(myFile.read());
      }
      // ==close the file:==
      myFile.close();  //закрыть файл
    }
  }
  return result;
}
/*
  macro_str = "";
  File_patch = "R_TIME/";
  File_patch = (char *)File_patch.c_str();
  File_name =  File_patch + "DAILY.TXT";     //выделили номер кнопки
  Serial.println("MACRO - " + File_name);
  if (SD.exists((char *)File_name.c_str()) == true) {  //есть такой файл
    myFile = SD.open(File_name);                       //открыть  файл
    if (myFile) {
      while (myFile.available()) {
        macro_str +=  char(myFile.read());
      }
      //razdel_macro();  //извлечь первую команду на исполненние
      Serial.println("DAILY - " + macro_str);
      // ==close the file:==
      myFile.close();  //закрыть файл
    }
  }
 */


void razdel_macro() {            //вся информация в строке macro_str
  // &#samsung=273009c4;sec=10;   //один раз с задержкой 10 сек
  // &#192.168.0.111/?map=cb4=1;sec=3;
  //byte ip[] = { 192, 168, 0, 121 }; //2016-1
  boolean fl_ip;
  ind1 = macro_str.indexOf("&#"); //найти начало строки
  if (ind1 >= 0 ) {
    macro_str.remove(0, ind1 + 2);  //вышли на начало
    ind1 = macro_str.indexOf("&#"); //найти начало след строки
    if (ind1 > 0) {  //есть еще строка
      cod_note = macro_str.substring(0, ind1);  //выделили команду из макро команды
      macro_str.remove(0, ind1);                //удаляем команду
    } else {
      cod_note = macro_str;
    }
    //192.168.0.121/?map=cb4=1;sec=3;    или       samsung=273009c4;sec=10
    ind2 = cod_note.indexOf("/?");       //признак передачи параметров на другой сервер
    if (ind2 > 0) {                      //GET http://www.site.ru/news.html HTTP/1.0\r\n
      fl_ip = true;                      //передача по WiFi на статический IP адрес
      //======получение ip[]=============================
      priem = cod_note;
      priem.remove(ind2, priem.length());//ip в чистом виде 192.168.0.121
      cod_note.remove(0, ind2);        //на будущее оставили /?map=cb4=1;sec=3;
      convert_ip(priem);
      //==================================================
      Serial.println();
      Serial.print("macro "); Serial.print(ip[0]); Serial.print("."); Serial.print(ip[1]); Serial.print(".");
      Serial.print(ip[2]); Serial.print("."); Serial.print(ip[3]);
      Serial.println(cod_note);

      ind2 = cod_note.indexOf(";");
      priem = cod_note.substring(0, ind2);  //записали запрос  /?map=cb4=1
      cod_note.remove(0, ind2 + 1);                //вышли на sec=3;

    } else {              //samsung=273009c4;sec=10
      Serial.println("macro2 " + DD2);

      fl_ip = false;
      ind2 = cod_note.indexOf(";");
      priem =  "/?" +  cod_note.substring(0, ind2);
      //ind1 = cod_note.indexOf('=');
      //pult_name =  cod_note.substring(0, ind1);        //??? пока на всякий случай
      //Serial.println("NAME " + pult_name + "=" +  priem);
      cod_note.remove(0, ind2 + 1);               //вышли на имя пульта - samsung=273009c4;t=10;
    }
    ind1 = cod_note.indexOf("sec");
    ind2 = cod_note.indexOf("=");
    cod_note.remove(ind1, ind2 + 1);                //вышли на sec=3;
    ind2 = cod_note.indexOf(";");
    count_timer = cod_note.substring(0, ind2).toInt();
    if (fl_ip == false) {
      init_mess();  //исполнить - priem
    } else {
      if (client.connect(ip, 80)) {
        Serial.println("connected");
        //ok      client.println("GET /search?q=arduino HTTP/1.0");
        client.println("GET " + priem + " HTTP/1.1");
        client.println();
      } else {
        Serial.println("connection failed");
      }
    }
  } else { //нет больше команд
    count_timer = 0;
  }
}

void convert_ip(String ip4) {
  for (int i = 0; i < 3; i++) {
    ind2 = ip4.indexOf('.');     //192.168.0.   121/?
    ip[i] =  ip4.substring(0, ind2).toInt();
    ip4.remove(0, ind2 + 1);
    //Serial.println(cod_note);  //test
  }
  ip[3] =  ip4.toInt();
}

void init_mess() { //вся информация в строке priem
  //формат priem: /?pultNAME=FFEEAADDCC      или /?ESP8266=cbn=1
  ind1 = priem.indexOf("?");                 //граница начала данных определена в начале подпрограммы
  ind2 = priem.indexOf("HTTP");              //граница (может быть и другая - \r)
  mess = priem.substring(ind1 + 1, ind2 - 1);//cb2  (=0) или cbA0
  if (change_fl_rec(mess) == false) {
    ind1 = priem.indexOf("?");               //граница начала данных определена в начале подпрограммы
    ind2 = priem.indexOf(":");               //вырезаем из оригинала
    pult_name = priem.substring(ind1 + 1, ind2);
    if (pult_name == "ESP8266") {            //запросы с пультов WIFI идут по IP адресу и сюда не попадают
      com_8266();
    } else {
      if (pult_name == "macro") {
        Serial.println("******");
        read_macro();                     //считать команду c SD  ?samsung;e13dda28=1,2000//один раз с задержкой 2 сек
        //        if (macro_str != ""){
        //          rasdel_macro();       //может переполнить стек, просто установлю fl_macro=true для исполнения в loop
      } else {
        if (pult_name == "map") {
          Serial.println("*map*" + par_map);
          form_mess(par_map, -1, -1);
        } else {
          if (pult_name == "rozetka") {
            form_mess(par_rozetka, -1, -1);                 //считать команду c SD  ?samsung;e13dda28=1,2000//один раз с задержкой 2 сек
          } else {
            if (pult_name == "daily") {
              if (priem.indexOf("zz") < 0) {
                if (priem.indexOf("ex_r") >= 0) {
                  priem.replace('@', '=');
                  ind1 = priem.indexOf('=');
                  String tt_par = "&#" + priem.substring(ind1 + 1, priem.indexOf(" HTTP"));

                  r_h = priem.substring(priem.indexOf("=") - 1, priem.indexOf("=")).toInt();
                  Serial.println();
                  Serial.println(tt_par);
                  switch (r_h) {
                    case 1: {
                        ex_DD1 = tt_par;
                        r_time = digit_time(tt_par);
                        break;
                      }
                    case 2: {
                        ex_DD2 = tt_par;
                        break;
                      }
                    case 3: {
                        ex_DD3 = tt_par;
                        break;
                      }
                    case 4: {
                        ex_DD4 = tt_par;
                        break;
                      }
                    default:
                      // if nothing else matches, do the default
                      // default is optional
                      break;
                  }

                }
              }
              par_daily = param_mess("daily", out_daily, '%'); //раз было обращение - стоит обновить
              form_mess(par_daily, -1, -1);
            } else {
              int num_str;
              if (pult_name == "transponder") {        //наш пульт
                if (priem.indexOf("zz") < 0) {         //это не запрос параметров
                  if (priem.indexOf("ex_r") >= 0) {    //это передача адресов!
                    ind1 = priem.indexOf("192");
                    ind2 = priem.indexOf(';');
                    String tt_par =  priem.substring(ind1, priem.indexOf(" HTTP"));
                    num_str = priem.substring(priem.indexOf("=") - 1, priem.indexOf("=")).toInt(); //номер однозначной переменной
                    //Serial.println(tt_par);
                    ex_TD[num_str] = tt_par;
                    //обновить IPадреса
                  } else {
                    //Serial.println("priem2 " + priem);
                    ind1 = priem.indexOf("ir");
                    if (ind1 >= 0) {                     //это передача разрешений [0-4]
                      priem.remove(0, ind1 + 2);
                      ind1 = priem.indexOf('=');
                      num_str = priem.substring(0, ind1).toInt(); //номер однозначной переменной
                      sos = priem.substring(ind1 + 1, ind1 + 2).toInt();
                      cb_TD[num_str] = sos;
                      //Serial.println("***zz***");
                      //Serial.print("cb_TD["); Serial.print(num_str); Serial.print("]="); Serial.print(cb_TD[num_str]);
                    }
                  }
                  //был запрос на изменение параметров
                  save_config();   //записать изменения
                  test_wifi();               //чтобы не потерять время подготовить для следующего запроса
                  //boot_config(); //были изменения - загружается только один раз, потом только изменяется
                } else {                                  //запрос параметров
                  mess = "";
                  for (int i = 0; i < 5; i++) {
                    mess = mess + "&ir" + String(i) + "=" + String(cb_TD[i]);
                  }
                  mess = mess + mess_wifi;
                  out_mess(mess);            //передача после любого запроса от клиента
                  Serial.println();
                  Serial.println("mess: " + mess);
                  test_wifi();               //чтобы не потерять время подготовить для следующего запроса
                }
              } else {
                //запрос от IR пульта
                com_IR();                         //выполнить
              }
            }
          }
        }
      }
    }
  }
  // mess =  pult_name + " :**!**: " + mess;     //???
  // Serial.println(mess);
}

void form_mess(String m_str, int spin, int ssos) {
  //===========new ex===============
  // если делать универсальный анализ
  if (ex_ip != "") {
    mess = "?result=&ex_out=" + m_str;
  } else {
    if (spin != -1) {
      mess = "Get result=&ex_out=" + m_str;
    } else {
      mess = "Get " + m_str; //для ответов на внешние запросы
    }
  }
  //mess = "Get result=&ex_out=" + m_str; //не должно быть пробелов!!!
  if (spin != -1) {
    if (spin != 20) {
      mess += spin;
    } else {
      mess += "A0";
    }
    if (ssos != -1) {
      mess += ", ";
      if (ssos == -2) {
        mess += "OUTPUT";
      } else {
        if (ssos == -3) {
          mess += "INPUT";
        } else    mess += ssos;
      }
    }
    mess += "); ";
  }
  out_mess(mess);
}

String param_mess(String par_str, String par_out, char ap) {
  String  result = "result=";
  //  Serial.println("par_out" + par_out);
  if (ap == '%') { //это точно daily
    //запишем ответ на запрос = предстоящие 4 команды
    par_str = ex_DD1.substring(2, ex_DD1.length());
    par_str += "%" +  ex_DD2.substring(2, ex_DD2.length());
    par_str += "%" +  ex_DD3.substring(2, ex_DD3.length());
    par_str += "%" +  ex_DD4.substring(2, ex_DD4.length());
    par_str += "%" + r_data;
  }

  //par_out строка, в которой через ; записано куда вписывать параметры
  //par_str буфер для хранения ответа на запрос
  while (par_out.indexOf(';') >= 0 ) {
    result += "&" + par_out.substring(0, par_out.indexOf(';')) + "=";
    par_out.remove(0, par_out.indexOf(';') + 1);
    String ert = par_str.substring(0, par_str.indexOf(ap));
    //    ert.replace('=', '@');
    if (ap == '@') {
      ert.replace('=', '@');
    } //это точно daily
    result +=  ert;
    //    result +=  par_str.substring(0, par_str.indexOf(ap));
    par_str.remove(0, par_str.indexOf(ap) + 1);
    /*
        result +=  par_str.substring(0, par_str.indexOf(';'));
        par_str.remove(0, par_str.indexOf(';') + 1);
    */
  }
  //Serial.println("result: " + result);
  return result;
}

void out_mess(String m_str)
{
  char c = client.read();
  //Serial.println("out: " + m_str);
  /*
    client = server.available(); //нельзя применять - теряем окно GET запроса
  */
  //  client.print(mess);
  //  delay(100);
  //ruben
  //mess = "HTTP / 1.1 200 OK\r\n";
  //mess += "Content - Type: text/html; charset=windows-1251\r\n\r\n";
  //mess += "Content - Type: Content-Type: text/html; charset=utf-8\r\n\r\n";  // text / html\r\n\r\n";
  //client.print(mess);
  //delay(10);
  client.print(m_str);
  // Serial.println(m_str);  //test
  delay(10);
  //cikl_current = 0; //задержка, чтобы избежать добавленных посылов по таймеру
}

void INTERRUPT_ok()
{
  switch (pin) {
    case 0: {
        break;
      }
    case 2: {
        break;
      }
    case 4: {
        digitalWrite(16,  HIGH);
        digitalWrite(2, LOW);
        delay(300);
        digitalWrite(16, LOW);
        digitalWrite(2,  HIGH);
        delay(300);
        digitalWrite(16,  HIGH);
        digitalWrite(2, LOW);
        delay(300);
        digitalWrite(16, LOW);
        digitalWrite(2,  HIGH);
        delay(300);
        digitalWrite(16,  HIGH);
        digitalWrite(2, LOW);
        delay(300);
        digitalWrite(16, LOW);
        digitalWrite(2,  HIGH);
        //      mess = "Get result=&out1=1941&out2=4&out3=t: РУБЕН";
        //      char c = client.read();                       //провоцирующее чтение
        //      out_mess( mess);
        break;
      }
    case 5: {
        break;
      }
    case 9: {
        break;
      }
    case 10: {
        break;
      }
    case 12: {
        break;
      }
    case 13: {
        break;
      }
    case 14: {
        break;
      }
    case 15: {
        break;
      }
    default:
      // if nothing else matches, do the default
      // default is optional
      break;
  }
  Serial.print("INTERRUPT ok - ");
  Serial.println(pin);
}


void page_index() {
  client.flush();
  for (int i = 0; i < 7; i++)        //грузим общий HEADER
  { mess = html_header[i];
    client.print(mess);
    delay( 10 );
  }
  //      client.print(Get_ip);
  for (int i = 7; i < 15; i++)        //грузим общий HEADER
  { mess = html_header[i];
    client.print(mess);
    delay( 10 );
  }

  mess =  macro;
  client.print(mess);
  pult_name = "macro";

  /*
      for (int i = 0; i < 3; i++) //индекс указателя массива всегда с НУЛЯ!!!
      { mess = html_samsung[i];
        client.print(mess);
        delay( 10 );
      }
      */
}
void control_wifi() {
  client.flush();

  for (int i = 0; i < 14; i++)
  { mess = html_esp[i];
    client.print(mess);
    delay( 10 );
  }
}

void com_IR() {
  //  Serial.println(mess);  //SAMSUNG=pn=samsung   GET /?wait:pn=samsung HTTP/1.1
  //  Serial.println("mess!!!!  " + mess);

  ind1 = mess.indexOf(':');        //wait:pn=samsung или  samsung:3
  cod_hex =  mess.substring(0, ind1); //
  //     Serial.println("cod_hex  " + cod_hex);
  mess.remove(0, ind1 + 1); //оставить только номер ноги
  //      Serial.println("mess!!!!  " + mess);
  ind1 = mess.indexOf('=');
  /*
    str_temp =  mess.substring(0, ind1);  //pn
    if (str_temp == "pn") {
  */
  if ( mess.substring(0, ind1) == "pn") {
    pult_name = mess.substring(ind1 + 1, mess.length()); //samsung
    if (cod_hex != pult_name) {
      mess = "Get result=&pultname=" + pult_name;
      out_mess(mess);
    }
    return;
  }
  //Serial.println("pult_name!!!!  " + pult_name);

  //сначала определить тип пульта - имена RF пультов начинаются с RF
  ind2 = mess.indexOf('RF');     //SAMSUNG=menu       GET /?samsung:3 HTTP/1.1
  if (ind2 > 0 || ind2 < 1) {    //ЭТО НЕ rf ПУЛЬТ
    pult_mode = "IR";
  } else {
    pult_mode = "RF";
  }

  File_patch = pult_mode + "/" + pult_name;
  File_patch = (char *)File_patch.c_str();  //убрать символы конца строки
  //Serial.println("File_patch  " + File_patch);

  ind1 = mess.indexOf(':'); //SAMSUNG=menu
  mess.remove(0, ind1 + 1);
  key_name = mess;
  // Serial.println("key_name  " + key_name);
  //Serial.println("IR comand - " + key_name);  //имя кнопки. Теперь можно сканировать не вводя, а указыва имя кнопки
  File_name =  File_patch + "/" + mess + ".raw";
  //Serial.println("File_name  " + File_name);
  if (SD.exists((char *)File_name.c_str()) == true) {  //есть такой файл
    myFile = SD.open(File_name);                       //открыть  файл
    if (myFile) {
      //Serial.println("File_open  ok");
      cod_len = 0;
      while (myFile.available()) {
        irSignal[cod_len] = myFile.read();
        cod_len += 1;
      }
      // ==close the file:==
      myFile.close();  //закрыть файл
      cod_len = cod_len - 2; //не учитывать два последних байта 0,13
      Serial.print("byte = "); Serial.println(cod_len);
      send_ircod();  // исполнить
      /*
            // =====start test=======================
              for (int i = 0; i < 69; i++) {
                Serial.print(irSignal[i], DEC); Serial.print(" ");
              }
              Serial.println();
            //=====end test=======================
      //            irsend.sendRaw(irSignal, sizeof(irSignal) / sizeof(irSignal[0]), cod_khz); //Note the approach used to automatically calculate the size of the array.
            irrecv.enableIRIn(); // разрешить receiver
            irrecv.resume();     // принять следующий код
      */
    }
  }
  /* RUBEN ИМЯ ПУЛЬТА
  mess = "Get result=&pultname=KUKU";
  out_mess(mess);
  */
}

String temp_str = "";
//подпрограмма формирования последовательности импульсов и передачи на излучатель
void send_ircod() { //max_len - запиши значение, чтобы не было ошибки компиляции
  unsigned  int int_temp;
  temp_str = "";
  for (int i = 0; i <= cod_len; i++) {
    //без следующей проверки программа сбрасывается,
    //так как "- 45" исключает нулевые значения irSignal[i]
    if (irSignal[i] != 0) {
      int_temp = irSignal[i];
      if (i % 2) { //по модулю 2 - четные // Mark
        //irSignal[i] = int_temp * USECPERTICK + MARK_EXCESS; //вариант разработчиков библиотеки
        //irSignal[i] = int_temp * USECPERTICK + MARK_EXCESS / 2; //максимально близкий к оригиналу SONY
        irSignal[i] = int_temp * USECPERTICK; //оптимальный
      }
      else { //нечетные // Space
        //irSignal[i] = int_temp * USECPERTICK - MARK_EXCESS; //вариант разработчиков библиотеки
        //irSignal[i] = int_temp * USECPERTICK - 150; //максимально близкий к оригиналу SONY
        irSignal[i] = int_temp * USECPERTICK - 45; //оптимальный подогнал по осциллографу
      }
    }
    temp_str += String(irSignal[i]) + ';';
  }


  Serial.print("cod_len ");
  Serial.println(cod_len);
  Serial.print("temp_str ");
  Serial.println(temp_str);
  for (int i = 0; i < 2; i++) { //передать команду пакетом три раза со своего модуля
    //irsend.sendRaw(irSignal, sizeof(irSignal) / sizeof(irSignal[0]), 38); //cod_khz = 38;  //частота
    irsend.sendRaw(irSignal, cod_len, cod_khz); //cod_khz = 38 (34.48);/cod_khz = 40 (37.04);  //частота
  }
  //******************
  send_irwifi();//****
  //******************
  irrecv.enableIRIn(); // разрешить receiver
  irrecv.resume();     // принять следующий код

}

void send_irwifi() { //max_len - запиши значение, чтобы не было ошибки компиляции
  int int_temp;
  //Serial.print("send_irwifi");
  if (cb_TD[0] == true) {
    for (int i = 1; i < 5; i++) {
      if (cb_TD[i] == true) {
        //преобразовать IP
        ex_ip = ex_TD[i].substring(0, ex_TD[i].indexOf(';'));
        Serial.println("ex_ip " + ex_ip);
        convert_ip(ex_ip);
        //        temp_str = "&ip=192.168.0.166;result=&rate=" + String(cod_khz) + "&code_len=" + String(cod_len) + "&ir=" + temp_str;
        if (client.connect(ip, 80)) {
          client.print("&ip=192.168.0.166;result=&rate=" + String(cod_khz) + "&code_len=" + String(cod_len) + "&ir=" + temp_str);//temp_str);
          client.println();
        } else {
          Serial.println("IP #" + String(i) + " connection failed");
        }
      }
    }
  }
  irrecv.resume();     // принять следующий код
}

void test_wifi() { //max_len - запиши значение, чтобы не было ошибки компиляции
  mess_wifi = "";
  for (int i = 1; i < 5; i++) {
    //преобразовать IP
    ex_ip = ex_TD[i].substring(0, ex_TD[i].indexOf(';'));
    //Serial.println("ex_ip " + ex_ip);
    convert_ip(ex_ip);
    String key_wifi = "---";
    if (cb_TD[i] == true) { //проверяем только разрешенные для уменьшения времени реакции
      if (client.connect(ip, 80)) {
        key_wifi = "ok-";
      }
    }  //проверяем только разрешенные
    mess_wifi = mess_wifi + "&ex_r" + String(i) + "=" + key_wifi + ex_TD[i];
  }
}

void com_8266()
{
  //=================RESET======================
  /*
  delay(2000);
  void(* resetFunc) (void) = 0; // Reset MC function
  resetFunc(); //вызов
   */
  String    zapros;
  //Serial.println(mess);   //ESP8266:cb2=0
  ind1 = mess.indexOf(':'); //ESP8266:cbi=1
  String vetka =  mess.substring(ind1 + 1, ind1 + 3);
  mess.remove(0, ind1 + 3);
  //Serial.println("3 mess- " + mess);
  zapros = mess;
  ind1 = zapros.indexOf('='); //такой же индекс и для mess
  ind2 = zapros.indexOf('\n');
  zapros = zapros.substring(ind1 + 1, ind2); //значение (sos)
  mess.remove(ind1, ind2);                   //оставить только номер ноги
  //Serial.println("4 zapros - " + zapros);
  sos = zapros.toInt();              //преобразовали значение в цифру
  Serial.println("ess - " + mess);
  if (mess != "A0") {
    //Serial.println("pin mess- " + mess);
    pin = mess.toInt();                     //Аналоговый вход A0 = 20
  } else {
    pin = 20;
  }
  if (vetka == "cb")          //on/off
  {
    if (pin != 20) {
      if (Key_output > 0)
      {
        pinMode(pin, OUTPUT);
        analogWrite(pin, 0); //для режима прямого управления
      }
      digitalWrite(pin, sos);
      mess = "digitalWrite(GPIO";
      form_mess(mess, pin, sos);
    } else {
      if (sos == 0) {
        pinMode(A0, OUTPUT);  //??
        mess = "pinMode(";
        form_mess(mess, pin, -2); //-2 OUTPUT
      } else {
        pinMode(A0, INPUT);
        mess = "pinMode(";  //-3 INPUT
        form_mess(mess, pin, -3);
      }
    }
  } else {
    if (vetka == "cd") {    //potenciometr
      if (pin != 20) {
        if (Key_output > 0)
        {
          pinMode(pin, OUTPUT);
        }
        analogWrite(pin, sos);
        mess = "analogWrite(GPIO";
        form_mess(mess, pin, sos);
      } else {
        //analogWrite(A0, sos);
      }
    } else {
      if (vetka == "sd")     //mode
      {
        switch (sos)
        {
          case 0: {
              if (pin == 20) {
                pinMode(A0, INPUT);
                mess = "pinMode(";
                form_mess(mess, pin, -3);
              } else {
                pinMode(pin, INPUT);
                mess = " pinMode(GPIO";
                form_mess(mess, pin, -3);
              }
              break;
            }
          case 1: {
              if (pin == 20) {
                pinMode(A0, OUTPUT);
                mess = "pinMode(";
                form_mess(mess, pin, -2);
              } else {
                pinMode(pin, OUTPUT);
                mess = " pinMode(GPIO";
                form_mess(mess, pin, -2);
              }
              break;
            }
          default: {
              Serial.println("mode ERROR - " + pin);
              break;
            }
        }
      } else {
        if  (vetka == "ci") {   //INTERRUPT
          switch (sos)
          {
            case 0: {
                detachInterrupt(digitalPinToInterrupt(pin));
                mess = "Interrupt disabled: GPIO";
                form_mess(mess, pin, -1);

                Serial.print("Interrupt disabled: pin ");
                Serial.println(pin);
                break;
              }
            case 1: {
                attachInterrupt(digitalPinToInterrupt(pin), INTERRUPT_ok, CHANGE);
                mess = "Interrupt enabled: GPIO";
                form_mess(mess, pin, -1);
                Serial.print("Interrupt enabled: pin ");
                Serial.println(pin);
                break;
              }
            default: {
                Serial.println("Interrupt ERROR - " + pin);
                break;
              }
          }
        } else {
          if (vetka == "ce") {
            Key_output = sos;               //_mess("Get result = &out2 = 34 & out1 = 19 & out3 = test");
            if (sos == 0) {
              mess = "avtooutput turn - off = "; //знак равно автоматически выбросится из параметров
            }
            else {
              mess = "avtooutput turn - on = "; //знак равно автоматически выбросится из параметров
            }
            form_mess(mess, sos , -1);
          } else {
            if (vetka == "tt") {
              Key_avto = sos;
              if (sos == 0) {
                mess = "timer off = "; //знак равно автоматически выбросится из параметров
              }
              else {
                mess = "timer on = ";  //знак равно автоматически выбросится из параметров
              }
              form_mess(mess, sos , -1);
            } else {
              if (vetka == "zz") { //запрос параметров
                client.flush();
                // char c = client.read();                       //провоцирующее чтение
                sos = analogRead(A0);
                mess = "Get result=&out1=" + String(analogRead(A0)) + "&out2=19&out3= t: "; //проверка передачи
                //mess = "Get result=&out1=13&out2=19&out3= t: "; //проверка передачи
                mess += cikl_current;                         //параметров
                cikl_current += 1;
                out_mess(mess);            //передача после любого запроса от клиента
                mess = "&cb16=" + String(digitalRead(16)) + "&cb5=" + String(digitalRead(5)) + "&cb4=" + String(digitalRead(4)) + "&cb0=" + String(digitalRead(0)) + "&cb2=" + String(digitalRead(2));
                out_mess(mess);            //передача после любого запроса от клиента
                mess = "&cb14=" + String(digitalRead(14)) + "&cb12=" + String(digitalRead(12)) + "&cb13=" + String(digitalRead(13)) +
                       "&cb15=" + String(digitalRead(15)) + "&cb3=" + String(digitalRead(3))  + "&cb1=" + String(digitalRead(1));
                out_mess(mess);            //передача после любого запроса от клиента
                if (digitalRead(10) > 0) {
                  mess = "&cb10=0";
                } else {
                  mess = "&cb10=1";
                }
                if (digitalRead(9) > 0) {
                  mess = mess + "&cb9=0";
                } else {
                  mess = mess + "&cb9=1";
                }
                //mess ="&cb10="+String(digitalRead(10))+"&cb9="+String(digitalRead(9));
                out_mess(mess);            //передача после любого запроса от клиента

              } else {
                if (vetka == "pn") {//запрос имени пульта при первой загрузке
                  //???
                }

              }
            }
          }
        }
      }
    }
  }   //   out_mess(mess); //ruben

  // Serial.println("vetka - " + vetka + ", pin - " + pin + ", vol - " + sos);
}

void serialEvent() {

  while (Serial.available()) {
    // get the new byte:
    char inChar = (char)Serial.read();
    // add it to the inputString:
    // if the incoming character is a newline, set a flag
    // so the main loop can do something about it:
    if (inChar == 255 || inChar == '\n') {
      stringComplete = true;
    } else { //чтобы не записывать код перевода строки
      inputString += inChar;

    }
  }
}

void boot_config() {  //файл загружается только при включении, потом только редактируется.
  mess = time_macro("", "config.ini");
  String test_str;
  for (int i = 1; i < 5; i++) {
    ind1 = mess.indexOf("&#ex_TD");  //&#ex_TD - метка строки в файле
    if (ind1 >= 0) {           //еще есть запись
      mess.remove(0, ind1 + 7); //чтобы не мешалось
      ind1 = mess.indexOf('=');
      ind2 = mess.indexOf('\n');
      ex_TD[i] = mess.substring(ind1 + 1, ind2);

      mess.remove(0, ind2);
      Serial.println("ex_TD = " + ex_TD[i]);
    }
  }
  for (int i = 0; i < 5; i++) {
    ind1 = mess.indexOf("&#cb_TD");
    if (ind1 >= 0) {           //еще есть запись
      mess.remove(0, ind1 + 7); //чтобы не мешалось
      ind1 = mess.indexOf('=');
      ind2 = mess.indexOf('\n');  //конец строки
      cb_TD[i] = mess.substring(ind1 + 1, ind2).toInt();
      mess.remove(0, ind2);
      //Serial.print("cb_DD[");Serial.print(i);Serial.print("] = ");Serial.println(cb_DD[i]);
    }
  }
  test_wifi();               //чтобы не потерять время подготовить для следующего запроса
  //client.connect(my_ip, 80)); //не обязательно, так как изменил ip на my_ip
  //Serial.println("Init SD" + mess);
}

void save_config() {
  File_name = "config.ini";
  if_exists();  //удалить старый
  myFile = SD.open(File_name, FILE_WRITE);   //создать файл
  if (myFile) {
    for (int i = 1; i < 5; i++) {
      myFile.print("&#ex_TD[");
      myFile.print(String(i));
      myFile.print(String("]="));
      myFile.println(ex_TD[i]);
    }
    for (int i = 0; i < 5; i++) {
      myFile.print("&#cb_TD[");
      myFile.print(String(i));
      myFile.print(String("]="));
      myFile.println(String(cb_TD[i]));
    }
    //  myFile.println();
    myFile.close();
  }
}

int decode_int(char ch1, char ch2) {
  //123%D0%B6%D0%96zZ == 123жЖzZ
  int result;
  if (ch1 > 57) {                 //D from D0
    result = (ch1 - 55) * 16;
  } else {
    result = (ch1 - 48) * 16;
  }
  if (ch2 > 57) {
    result = result + (ch2 - 55);
  } else {                        //0 from D0
    result = result + (ch2 - 48);
  }
  return result;
}

String decode_(String mm) {
  String result_win = "";
  String result = "";
  char inChar;
  mm.replace("%20", " ");
  for (int i = 0; i < mm.length(); i++) {
    inChar = mm[i];
    if (inChar == '%')  {
      ind2 = decode_int(char(mm[i + 1]), char(mm[i + 2]));
      //Serial.print(":ind2:" );
      //Serial.println(ind2);
      if (ind2 == 208) { //вылавливаем русский код
        result += char(ind2);  //utf-8 - первый байт
        ind2 = decode_int(char(mm[i + 4]), char(mm[i + 5])); //чтение второго байта после %
        result += char(ind2);  //utf-8 -второй байт
        //ind2 = decode_int(char(mm[i + 4]), char(mm[i + 5])) + 48;
        ind2 += 48; //win-1251
        i += 3;          //прошли еще три символа
      } else {
        if (ind2 == 209) { //вылавливаем русский код, начиная с р
          result += char(ind2);  //utf-8 - первый байт
          ind2 = decode_int(char(mm[i + 4]), char(mm[i + 5]));  //чтение второго байта после %
          result += char(ind2);  //utf-8 -второй байт   + 112
          ind2 += 112; //win-1251
          i += 3;        //прошли еще три символа
        }
      }
      result_win += char(ind2);
      i += 2;            //прочли группу с первыми двумя символами
    } else {             //все остальные, кроме русских, символы
      result += inChar;
      result_win += inChar;
    }
  } //преобразовали в Win1251, теперь в utf-8
  return result;
}

void initHardware()
{
  irsend.begin();
  Serial.begin(9600);
  Serial.print("ESP8266 pult ver. 4.03 - decoding utf-8");
  Serial.print("\nInitializing SD card...");
  if (!SD.begin(SS)) {
    //if (!SD.begin(15)) { //это нога (15), (SS) - автоматический поиск pin
    Serial.println("init SD failed!");
    return;
  }
  Serial.println("init SD done.");
  boot_config();
  //-===- установка времени
  //  setTime(11, 01, 00, 22, 6, 2016); //12 час. 45 мин. 30 сек. 3.06.2016
  //  RTC.set(now());

  /*
    // Activate RTC module
    digitalWrite(DS1302_GND_PIN, LOW);
    pinMode(DS1302_GND_PIN, OUTPUT);

    digitalWrite(DS1302_VCC_PIN, HIGH);
    pinMode(DS1302_VCC_PIN, OUTPUT);

    Serial.println("RTC module activated");
    Serial.println();
    delay(500);
    setTime(3, 24, 00, 28, 5, 2016); //17 час. 1 мин. 30 сек. 23.05.2016
    RTC.set(now());
    Serial.println(RTC.get());
    if (RTC.haltRTC()) {
      Serial.println("The DS1302 is stopped.  Please run the SetTime");
      Serial.println("example to initialize the time and begin running.");
      Serial.println();
    }
    if (!RTC.writeEN()) {
      Serial.println("The DS1302 is write protected. This normal.");
      Serial.println();
    }
    */
}


