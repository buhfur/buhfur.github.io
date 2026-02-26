---
title: Asterisk 
parent: Misc
grand_parent: Linux
nav_order: 100
layout: default
---


# Terminology 

All the terms below are in the context of telecommunications and Asterisk respectively. 


dialplan:  Call routing logic. 
```bash
[context]
exten => extension,priority,application(arguments)
```

# Modules 

* loadable components 

* channel drivers ( chan\_pjsip.so )

* Modules loaded based on /etc/asterisk/modules.conf file 


## Types of Modules 

### Applications

Dial() : Responsible for making outgoing connections to external resources 

Voicemail() 

Playback() 

Queue() 


### Misc

• Call detail recording (CDR) modules

• Bridging Modules - Mechanisms that connect channels to each other 

• Channel event logging (CEL) modules

• Channel drivers—Various connections into and out of the system; SIP (Session

Initiation Protocol)messaging uses the PJSIP channel drivers
• Codec translators—Convert various codecs such as G729, G711, G722, Speex,

and so forth
• Format interpreters—As above, but relating to files stored in the filesystem

• Dialplan functions—Enhance the capabilities of the dialplan

• PBX modules

• Resource modules

• Add-on modules

• Test modules

# Applications 

* Dialplan applications are used in the extensions.conf file which define actions that can be applied to a call  

## Call Detail Recording Modules  

* CDR's can be stored to a file ( default ) , database , RADIUS, or syslog 

```bash
cdr_adaptive_odbc: Allows writing of CDRs through ODBC framework with ability to add custom fields

cdr_csv: Writes CDRs to disk as a comma-separated values (CSV) file

cdr_custom: Writes CDRs to a CSV file, but allows addition of custom fields

cdr_odbc: Writes CDRs through ODBC framework

cdr_syslog: Writes CDRs to syslog
```

## Channel Drivers 

* Without these , asterisk would not be able to make or receive calls 

* channel drivers are specific to protocol ( SIP, ISDN, etc)

```bash
chan_bridge: Used internally by the ConfBridge() application; should not be used directly

chan_dahdi: Provides connection to PSTN cards that use DAHDI channel drivers

chan_local: Provides a mechanism to treat a portion of the dialplan as a channel

chan_motif: Implements the Jingle protocol, including the ability to connect to Google Talk and Google Voice; introduced in Asterisk 11

chan_multi: Provides connection to multicast Realtime Transport Protocol (RTP) streams

cast_rtp

chan_pjsip: Session Initiation Protocol (SIP) channel driver
```

## Codec Translators 

The codec3 translators (often called transcoders) allow Asterisk to convert audio stream formats between calls. So if a call comes in on a PRI circuit (using G.711) and needs to be passed out a compressed SIP channel (e.g., using G.729, one of many codecs that SIP can handle), the relevant codec translator would perform the conversion.

> If a codec (such as G.729) uses a complex encoding algorithm, heavy use of transcoding can place a massive burden on the CPU.  Specialized hardware for the decoding/encoding of G.729 is avail‐ able from hardware manufacturers such as Sangoma and Digium (and likely others)

```bash
codec_alaw: A-law PCM codec used all over the world on the PSTN (except Canada/USA). This codec (along with
ulaw) should be enabled on all your channels.

codec_g729: Was until recently a patented codec, but is now royalty-free. As of this writing it is still sold by Digium
as an add-on, but it can also be found as a free package. It’s a very popular codec if compression is
desired (and CPU use is not an issue), but it imposes load on the CPU, adds latency to calls, reduces
quality slightly, and will not reduce overhead in any way.

codec_a_mu: A-law to mu-law direct converter.

codec_g722: Wideband audio codec.

codec_gsm: Global System for Mobile Communications (GSM) codec. Very poor sound quality.

codec_ilbc: Internet Low Bitrate Codec.

codec_lpc10: Linear Predictive Coding vocoder (extremely low bandwidth).

codec_opus: Intended to replace speex (and vorbis).

codec_resample: Resamples between 8-bit and 16-bit signed linear.

codec_speex:  Speex codec.

codec_ulaw:  Mu-law PCM codec used on PSTN in Canada/USA. It’s more formally written as μ-law, but not many
people have a Greek letter μ on their keyboard, so it’s popularly written as ulaw.a This is often the
default codec, and should be enabled on all your channels.
```
