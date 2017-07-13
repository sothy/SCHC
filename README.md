Static Context Header Compression (SCHC, pronounced chic) is a *generic* compression mechanism for LPWAN networks. It is standardized at the IETF.

This implementation applies SCHC to CoAP/UDP/IPv6 headers, but can be extended to other type of fields.

# Introduction

This document describes the application of generic compression/decompression processes defined in {{I-D.toutain-lpwan-ipv6-static-context-hc}} and {{I-D.toutain-lpwan-coap-static-context-hc}}.

For the moment, the implementation has only been tested uplink and downlink, communicating from the Device (Dev) to the LPWAN Compressor/Decompressor (SC) in the infrastructure with a simple CoAP server running on top of the SCHC C/D. Device implementation is 
writen in Python. It currently use the pycom API for LoRa and can be easily adapted for Sigfox.

A CoAP/UDP/IPv6 packet is generated by the Device, the Device parses this packet to obtain each value of the header fields, then the Device searches for a compression rule that matches this packet format. If there is a rule in the context that matches the header format and values of the packet, the header fields are compressed using the Compression/Decompression Action (CDA) specified by that rule and the compressed packet is sent over the air to the infrastructure C/D.

Upon receipt, the compresssed packet is parsed by the infrastructure C/D to delineate the rule identifier (first byte), each subsequent header field (as specified by the rule) and the payload. Finally, the LC decompresses the headers, as described in the rule. The CoAP message is then processed and if an answer is needed, the response is compressed and sent back to the device which decompressed it.

For this purpose, the Device is implemented on a LoPy module, which is able to transmit via LoRa and can be easily programmed in MicroPython. The infrastructure C/S is implemented with a Node.js by an HTTP Server which receives the raw compressed packet LPWAN payload frames in a POST message. The answer is included in the Acknowledgement of this POST message.

# compression / decompression in Python