# enebular-agent-aws-iot

## Abstract

An agent which controls Node-RED instance and accepts deployment request from enebular.com via AWS IoT

## Setup & Run

1. Create a folder and setup Node.js project using `npm init`.

    $ mkdir enebular-agent-pj && cd enebular-agent-pj
    $ npm init

2. Install Node-RED and enebular-agent-aws-iot.

    $ npm install -S node-red enebular-agent-aws-iot

3. Create `stop.sh` file and paste the below code.

    ```bash
    $ #!/bin/bash
    touch temp.text
    lsof -n -i4TCP:$1 | awk '{print $2}' > temp.text
    pidToStop=`(sed '2q;d' temp.text)`
    > temp.text
    if [[ -n $pidToStop ]]
    then
    kill -9 $pidToStop
    echo "Congrates!! $1 is stopped."
    else
    echo "Sorry nothing running on above port"
    fi
    ```

4. Create `config.json` file and set information of the corresponding AWS IoT thing resource. See `config.json` file in the `example` directory.

5. Download AWS IoT certificate files in `certs` directory, which is registered for the thing device.

6. Create a `settings.js` file of Node-RED, which specifies user directory and flow file. See `settings.js` file in the `example` directory.

7. Run `enebular-agent-aws-iot` process with node-red startup command.

    $ ./node_modules/.bin/enebular-agent-aws-iot run ./node_modules/.bin/node-red -s settings.js

## Thing Attributes

- **power**: The value which represents Node-RED process status ("on" or "off"). When the value is "on" it indicates the device runs Node-RED process. To restart the Node-RED process in the device, first set "off" and then set to "on".

- **flows**: the JSON string which defines flows definition. 

- **creds**: the JSON string which defines credentials used in flows. After the change of creds in thing shadow it must be restarted (not automatically restarting the creds right now).

- **packages**: the JSON string which describes depending packages of the flow node to work.
