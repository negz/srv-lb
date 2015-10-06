[![Build Status](https://img.shields.io/codeship/b556c2e0-4dc7-0133-eaf7-524cf6105349.svg)](https://codeship.com/projects/106694)
[![GoDoc](http://godoc.org/github.com/benschw/srv-lb?status.png)](http://godoc.org/github.com/benschw/srv-lb/lb)


# SRV Record Load Balancer for Go

`SRV-Lb` is a load balancer designed for use with service discovery solutions
that expose a discovery interface of DNS SRV records
(e.g. [consul](https://consul.io/) or [skyDNS](https://github.com/skynetservices/skydns))


Selects a `SRV` record answer according to specified load balancer algorithm,
then resolves its `A` record to an ip, and returns an `Address` structure:

	type Address struct {
		Address string
		Port    uint16
	}


You can either default to using the resolv.conf from your system, specifying it 
when configuring the library, or set it as an ENV variable (e.g. `SRVLB_HOST=127.0.0.1:8600`)

## Example:
### Default Load Balancer

	srvName := "foo.service.fligl.io"
	lb := lb.New(lb.DefaultConfig(), srvName)

	address, err := lb.Next()
	if err != nil {
		panic(err)
	}

	fmt.Printf("%s", address.String())
	// Output: 0.1.2.3:8001

- Uses dns server configured in `/etc/resolv.conf`
- Uses round robin strategy


### or build a generic load balancer

	srvName := "foo.service.fligl.io"
	lbDriver := lb.NewGeneric(lb.DefaultConfig())

	address, err := lb.Next(srvName)
	if err != nil {
		panic(err)
	}
	
	fmt.Printf("%s", address.String())
	// Output: 0.1.2.3:8001

### or configure explicitely

	srvName := "foo.service.fligl.io"
	cfg := &lb.Config{
		Dns:      dns.NewLookupLib("127.0.0.1:8600"),
		Strategy: RoundRobin,
	}
	lb := lb.New(cfg, srvName)

	address, err := lb.Next()
	if err != nil {
		panic(err)
	}
	
	fmt.Printf("%s", address.String())
	// Output: 0.1.2.3:8001



## Development
tests are run against some fixture dns entries I set up on fligl.io (`dig foo.service.fligl.io SRV`).

	go get -u -t ./...
	go test ./...

	




## Development
tests are run against some fixture dns entries I set up on fligl.io (`dig foo.service.fligl.io SRV`).

	go get -u -t ./...
	go test ./...

	


