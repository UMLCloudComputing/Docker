# LXC Demo Commands 

`lxc-checkconfig`
* Verify that LXC is running

`lxc-create --name test_container --template download -- --dist <DIST> --release <VERSION> --arch amd64`
* Goto: [images.linuxcontainers.org](https://images.linuxcontainers.org/) 
* This will create the container you specify, it does *not* start it 

`lxc-start --name test_container`
* This will start the container