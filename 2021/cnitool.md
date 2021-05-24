# Testing your CNI

Kubernetes delegates networking to plugins that conform to the [CNI spec](https://github.com/containernetworking/cni/blob/master/SPEC.md).
CNI defines the protocol between the plugin binary and a runtime which maybe Kubelet.

A CNI spec conforming plugin binary receives information via stdin and environment variables.

[cnitool](https://github.com/containernetworking/cni/tree/master/cnitool) is a golang program to invoke your CNI binary easily with the required stdin data. For CNI tools, this data is stored in json format in a file in ```/etc/cni/net.d/```.
Environment vars required must be available in the shell before invoking CNI tool.

Using this tool will shorten your development and test loop.

For installation and configuration of the test netns, see the associated CNI tool readme.

## Invoking SRIOV CNI with CNI tool

Create a network configuration which cnitool will pass via stdin to SRIOV CNI
```
$ echo '{"deviceID": "0000:0a:01.0", "cniVersion": "0.4.0", "name": "mysriov", "type": "sriov"}' | tee /etc/cni/net.d/20-mysriov.conf
```
Note: mandatory addition of deviceID which must be a virtual function PCI address for SRIOV CNI
For additional network configuration options, see ```netconf``` struct and the associated struct field meta info tag name.

Invoke SRIOV CNI binary
```
$ CNI_PATH=/opt/cni/bin cnitool add mysriov ${path-to-test-netns}
```