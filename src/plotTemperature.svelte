<script>
	// Create a client instance
	import {version} from './stores';
	import {state} from './stores';
	var wsbroker = "wondrous-lifeguard.cloudmqtt.com";  //mqtt websocket enabled broker
	var wsport = 443 // port for above
	var client = new Paho.MQTT.Client(wsbroker, wsport, "myclientid_" + parseInt(Math.random() * 100, 10));
	
	// set callback handlers
	client.onConnectionLost = onConnectionLost;
	client.onMessageArrived = onMessageArrived;
	var options = {
	timeout: 3,
	onSuccess: onConnect,
	userName : "tester",
	password : "tester",
	useSSL: true,
	onFailure: doFail
}

// connect the client
client.connect(options);

// called when the client connects
function onConnect() {
	// Once a connection has been made, make a subscription and send a message.
	console.log("onConnect");
	//client.subscribe("/AirHeritage/"+ServerName+"/#");
	client.subscribe("sensorLab");
	Plotly.plot('TEMPchart', data);
	Plotly.newPlot('TEMPchart', data, layout);
}

function doFail(e){
	console.log(e);
}

// called when the client loses its connection
function onConnectionLost(responseObject) {
	if (responseObject.errorCode !== 0) {
		console.log("onConnectionLost:"+responseObject.errorMessage);
	}
}

			
var value;

var time = new Date();

var layout = {
  autosize: false,
  width: 250,
  height: 200,  margin: {
    l: 30,
    r: 10,
    b: 50,
    t: 50,
    pad: 0
  },
};
var data = [{
  x: [time], 
  y: [],
  mode: 'lines',
  line: {
	color: '#80CAF6',
	shape: 'spline',
	width: 2
	},
  type: 'scatter'	
}]


var cnt = 0;
// called when a message arrives
function onMessageArrived(message) {
	console.log("onMessageArrived:"+message.payloadString);
	const obj = JSON.parse(message.payloadString);
	value = obj.BME280_Temperature;
}	

var interval = setInterval(function() {
  
  var time = new Date();
  
  var update = {
  x:  [[time]],
  y: [[value]]
  }
  
  var olderTime = time.setMinutes(time.getMinutes() - 1);
  var futureTime = time.setMinutes(time.getMinutes() + 1);
  
  var minuteView = {
        xaxis: {
          type: 'date',
          range: [olderTime,futureTime]
        }
      };
  
	if ($state === "PlotTemperature") {
  Plotly.relayout('TEMPchart', minuteView);
  Plotly.extendTraces('TEMPchart', update, [0])
  }
  
  //if(cnt === 400) clearInterval(interval);
}, 250);

</script>

<section id="plotTemperature" class="visible">
<h3>Sensor Monitoring {$version}</h3>
<h4>Temperature [°C]</h4>
<div id="TEMPchart"></div>
</section>

