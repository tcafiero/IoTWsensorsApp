<script>
	// Create a client instance
	var wsbroker = "m21.cloudmqtt.com";  //mqtt websocket enabled broker
	var wsport = 22605 // port for above
	var client = new Paho.MQTT.Client(wsbroker, wsport, "myclientid_" + parseInt(Math.random() * 100, 10));
	
	// set callback handlers
	client.onConnectionLost = onConnectionLost;
	client.onMessageArrived = onMessageArrived;
	var options = {
	timeout: 3,
	onSuccess: onConnect,
	userName : "air-heritage",
	password : "new-age",
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
	client.subscribe("AirHeritage/PAR");
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

// called when a message arrives
function onMessageArrived(message) {
	console.log("onMessageArrived:"+message.payloadString);
	if(cnt == 0) {
		Plotly.plot('chart',[{
			y:[Number(message.payloadString)],
			type:'line'
		}]);					
		} else {
		Plotly.extendTraces('chart',{ y:[[Number(message.payloadString)]]}, [0]);
	}
	cnt++;
	if(cnt > 30) {
		Plotly.relayout('chart',{
			xaxis: {
				range: [cnt-30,cnt]
			}
		});
	}
}	

			
var cnt = 0;


</script>

<section id="PlotSection" class="visible">
<h1>PAR</h1>
<div id="chart"></div>
</section>
