# Windows 11 DNS Troubleshooting Lab

## What I Was Trying to Do

I wanted to practice identifying and troubleshooting a DNS issue in my Windows 11 VMware home lab.

Instead of waiting for a real DNS problem to happen, I intentionally configured CLIENT01 with a DNS server that would not respond so I could see what the failure looked like and work through it.

## Lab Setup

- VMware Workstation Pro
- Windows 11 Pro
- VM name: CLIENT01
- Network type: NAT
- IPv4 address: 192.168.58.128
- Default gateway: 192.168.58.2
- DNS configured manually for testing

## Baseline Testing

Before changing anything, I verified that CLIENT01 had working Internet connectivity and DNS resolution.

I tested:

`ping 8.8.8.8`

`ping google.com`

`nslookup google.com`

All three tests worked successfully.

This gave me a working baseline before intentionally creating the problem.

## Creating the DNS Problem

I opened the IPv4 properties for the Ethernet adapter and changed the DNS configuration from automatic to a manual DNS server:

`203.0.113.10`

I left the IP address configuration set to obtain an address automatically through DHCP.

The goal was to break name resolution without breaking CLIENT01's actual Internet connection.

## The Problem

After changing the DNS server, I tested connectivity again.

First, I ran:

`ping 8.8.8.8`

This still worked successfully with 0% packet loss.

Next, I ran:

`ping google.com`

This failed with:

`Ping request could not find host google.com.`

I then ran:

`nslookup google.com`

The DNS request timed out while trying to contact:

`203.0.113.10`

This showed that CLIENT01 could still reach the Internet by IP address, but it could no longer translate hostnames such as google.com into IP addresses.

## Diagnosis

The successful ping to `8.8.8.8` showed that the VM still had working network connectivity.

The failed `ping google.com` and failed `nslookup google.com` showed that the problem was specifically related to DNS.

This helped isolate the issue without assuming that the entire Internet connection was down.

## The Fix

I returned to the IPv4 settings for the Ethernet adapter and changed the DNS configuration back to:

`Obtain DNS server address automatically`

After restoring the correct DNS configuration, I ran:

`ipconfig /flushdns`

This cleared the local DNS cache so Windows would perform fresh DNS lookups instead of relying on previously cached information.

## Verifying the Fix

After correcting the DNS configuration, I tested the network again.

I ran:

`ping 8.8.8.8`

`ping google.com`

`nslookup google.com`

All three tests worked successfully again.

This confirmed that both Internet connectivity and DNS name resolution had been restored.

## What I Learned

This lab helped me understand the difference between general Internet connectivity and DNS resolution.

A computer can still have working Internet access while being unable to open websites or applications by name if DNS is not functioning correctly.

I also learned that testing an external IP address separately from a hostname is a useful way to narrow down a network problem.

If `ping 8.8.8.8` works but `ping google.com` fails, DNS should be one of the first things to investigate.

Some of the commands I practiced in this lab were:

- `ping`
- `nslookup`
- `ipconfig /flushdns`

This lab reinforced the importance of troubleshooting step by step instead of assuming that a user's report of "the Internet is down" means the entire network connection has failed.
