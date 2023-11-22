<script>
	import {version} from './stores';
	import {state} from './stores';
	// Create a client instance
	var wsbroker = "wondrous-lifeguard.cloudmqtt.com";  //mqtt websocket enabled broker
	var wsport = 443 // port for above
	var client = new Paho.MQTT.Client(wsbroker, wsport, "myclientid_" + parseInt(Math.random() * 100, 10));
	
	// set callback handlers
	client.onConnectionLost = onConnectionLost;
	client.onMessageArrived = onMessageArrived;
	var options1 = {
	timeout: 3,
	onSuccess: onConnect,
	userName : "air-heritage",
	password : "new-age",
	useSSL: true,
	onFailure: doFail
}

// connect the client
client.connect(options1);

// called when the client connects
function onConnect() {
	// Once a connection has been made, make a subscription and send a message.
	console.log("onConnect");
	// client.subscribe("/AirHeritage/"+ServerName+"/#");
	client.subscribe("sensorLab");
	Plotly.plot('PARchart', data);
	Plotly.newPlot('PARchart', data, layout);
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
			
var cnt = 0;


var value;

var time = new Date();

var layout = {
  autosize: true,
  margin: {
    l: 10,
    r: 10,
    b: 10,
    t: 10,
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
	value = obj.ePPFD;
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
  
	if ($state === 'PlotPAR') {
	
  Plotly.relayout('PARchart', minuteView);
  Plotly.extendTraces('PARchart', update, [0])
	}
 
}, 250);



</script>

<section id="PlotSection" class="visible">
<h1>Sensor Monitoring {$version}</h1>
<h2>Light [PPFD]</h2>
<div id="PARchart"></div>
</section>
