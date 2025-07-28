# Kube-Endoscope

Kube-Endoscope is a powerful observability tool designed to capture and analyze unencrypted traffic entering and leaving the kube-apiserver, leveraging deep eBPF instrumentation and real-time streaming. It provides fine-grained visibility into TLS traffic via dynamic probes and supports decoding of HTTP/2 and GZIP payloads. The system is composed of three key components:

## 1. eBPF Tracer

A Go-based binary called the tracer is deployed on nodes running kube-apiserver. It:

• Loads a custom eBPF program with uprobe and uretprobe hooks targeting key crypto/tls Go methods (WriteRecordLocked, readRecordOrCCS), as well as kernel-level TCP hooks.

• Utilizes multiple LRU hash maps and a perf buffer to efficiently correlate and emit TLS events, capturing rich context (e.g., socket info, fd-level metadata).

• Streams structured events to the hub using gRPC.

## 2. Event Hub

The hub is a Go-based gRPC server that:

• Receives and processes traffic events from the tracer.

• Assigns unique identifiers to events for deduplication and tracking.

• Performs stateful decoding of HTTP/2 frames and GZIP decompression where applicable.

• Persists events to Kafka in JSON format for durability and scalability.

• Broadcasts both historical and live events to connected WebSocket clients (e.g., the frontend).

• Kafka also enables synchronization across multiple hub instances.

## 3. Frontend Dashboard

A Vue.js SPA serves as the visual frontend, featuring:

• Real-time event display via WebSocket streaming.

• Interactive table powered by ag-grid-community.

• Detailed inspection of HTTP/2 frames, decompressed payloads, and TLS record metadata.

• Multiple display modes (e.g., hex, base64, ASCII) for raw event data.

## Key Features:

• Deep visibility into kube-apiserver traffic without needing to decrypt TLS externally.

• Efficient eBPF-based instrumentation with minimal performance overhead.

• Real-time and historical traffic visualization.

• Scalable architecture using Kafka for durability and horizontal coordination.

• Modular design supporting gRPC, Kafka, WebSockets, and Vue.js frontend.

## Demo:
[![](https://markdown-videos-api.jorgenkh.no/youtube/W_HJ4eimxNw)](https://youtu.be/W_HJ4eimxNw)
