# DNS

## Definition

DNS stands for Domain Name System. It is created to resolve IP address from a domain name.

## How it works

![DNS Components](images/dns.png)

First, when you type a domain name, eg. `yahoo.com`, in the browser, it will check if the IP corresponded to the domain name exists in its cache. If not, the browser will request the Resolver Server (located in ISP) to resolve the IP address.

Next, the Resolve Server check its cache for the IP address of `yahoo.com`. If the IP address doesn't exist in cache, the Resolver Server will ask the Root Server for the IP address.

The Root Server doesn't know any infomation about the IP address of the full domain name. It just stores the IP address of Top Level Domain (TLD) Servers, eg. `.com`, `.vn`, `.net`. The Root Server then returns the IP address of TLD Server which is responsible for `.com` domain to the resolver.

The TLD Server doesn't know the IP address of `yahoo.com`. Instead, it does know the IP address of Authorative Name Servers, which contains the record for the IP address of `yahoo.com`. TLD server then returns this IP address to the Resolver Server.

The Resolver Server then ask the Authorative Name Server for the IP address of `yahoo.com`. This server has the IP address record for `yahoo.com`, so it returns the IP address to the Resolver.

The Resolver Server may cache the IP address received before returns to it to the client. Similarly, when receives the IP address from Resolver Server, the browser may cache the IP address in its own cache.
