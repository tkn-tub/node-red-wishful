# node-red-uniflex

A node RED module that connects node RED to [UniFlex control framework](https://github.com/uniflex).

# Installation:
To install UniFlex framework with all available modules, please go through all steps in [manifest](https://github.com/uniflex/manifests) repository.

# Install node red framework:

    curl -sL https://deb.nodesource.com/setup_0.10 | sudo -E bash -
    sudo apt-get install -y nodejs
    sudo apt-get install -y build-essential
    sudo npm install -g --unsafe-perm node-red

# Install additional node-RED nodes:

    cd $HOME/.node-red
    sudo npm install zmq
    sudo npm install uniflex/node-red-uniflex
    sudo npm install node-red-node-smooth

# Example flow graph

	[{"id":"ef5bf96.f10a408","type":"tab","label":"Flow 1"},{"id":"827885a7.37ab88","type":"comment","z":"ef5bf96.f10a408","name":"Subscribe for SpectralScanSampleEvent","info":"","x":171,"y":133,"wires":[]},{"id":"8b85f74e.959248","type":"function","z":"ef5bf96.f10a408","name":"Create and Serialize Event","func":"//Create and serialize event\nvar eventType = 'AveragedSpectrumScanSampleEvent';\nvar data = {'avg': msg.payload};\nmsg.event = JSON.stringify(data);\n\n//Set topic\nmsg.topic = 'AveragedSpectrumScanSampleEvent';\n\n//Create Message Description\nvar jsonType = 1;\nmsg.msgType = eventType;\nmsg.sourceUuid = \"node-red-1\";\nmsg.serializationType = jsonType;\n\nreturn msg;","outputs":1,"noerr":0,"x":864,"y":337,"wires":[["bf29ca8d.40d638"]]},{"id":"8553f261.f5098","type":"smooth","z":"ef5bf96.f10a408","name":"Moving Average Filter","action":"mean","count":"10","round":"","x":608.5,"y":280,"wires":[["8b85f74e.959248"]]},{"id":"28907a2d.d76f86","type":"function","z":"ef5bf96.f10a408","name":"Parse Event","func":"//Parse event\nmsg.event = JSON.parse(msg.event);\n\n//Set to payload for next node on path\nmsg.payload = msg.event.sample;\n\nreturn msg;","outputs":"1","noerr":0,"x":402,"y":228,"wires":[["8553f261.f5098"]]},{"id":"bf29ca8d.40d638","type":"uniflex-pub","z":"ef5bf96.f10a408","name":"UniFlex-PUB","mode":"pub","uri":"tcp://127.0.0.1:8989","server":false,"x":1173,"y":405,"wires":[]},{"id":"75198dfc.8ae674","type":"uniflex-sub","z":"ef5bf96.f10a408","name":"UniFlex-SUB","mode":"sub","uri":"tcp://127.0.0.1:8990","server":false,"topics":"SpectralScanSampleEvent","x":155,"y":171,"wires":[["28907a2d.d76f86","f64543e6.276378"]]},{"id":"7955b93f.86aa48","type":"comment","z":"ef5bf96.f10a408","name":"Publish AveragedSpectralScanSampleEvent","info":"","x":1203,"y":373,"wires":[]},{"id":"f64543e6.276378","type":"debug","z":"ef5bf96.f10a408","name":"","active":true,"console":"false","complete":"false","x":360,"y":313,"wires":[]}]

![my_filter](./my_filter.png)

# Example UniFlex control program that interacts with node RED
	
	See UniFlex examples module, i.e. examples/node_red/
