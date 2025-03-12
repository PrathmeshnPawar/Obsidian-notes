## **Simulation Setup**

The following ns-3 simulation sets up a **point-to-point network** with a UDP client-server model to measure performance metrics.

### **Code Implementation**

```cpp
#include "ns3/core-module.h"
#include "ns3/network-module.h"
#include "ns3/internet-module.h"
#include "ns3/point-to-point-module.h"
#include "ns3/applications-module.h"
#include "ns3/flow-monitor-module.h"

using namespace ns3;

NS_LOG_COMPONENT_DEFINE("NetworkPerformance");

int main(int argc, char *argv[]) {
    uint32_t packetSize = 1024;
    uint32_t numPackets = 1000;
    double interval = 0.01;

    NodeContainer nodes;
    nodes.Create(2);

    PointToPointHelper pointToPoint;
    pointToPoint.SetDeviceAttribute("DataRate", StringValue("10Mbps"));
    pointToPoint.SetChannelAttribute("Delay", StringValue("2ms"));

    NetDeviceContainer devices = pointToPoint.Install(nodes);
    InternetStackHelper stack;
    stack.Install(nodes);

    Ipv4AddressHelper address;
    address.SetBase("10.1.1.0", "255.255.255.0");
    Ipv4InterfaceContainer interfaces = address.Assign(devices);

    uint16_t port = 9;
    UdpServerHelper server(port);
    ApplicationContainer serverApp = server.Install(nodes.Get(1));
    serverApp.Start(Seconds(1.0));
    serverApp.Stop(Seconds(10.0));

    UdpClientHelper client(interfaces.GetAddress(1), port);
    client.SetAttribute("MaxPackets", UintegerValue(numPackets));
    client.SetAttribute("Interval", TimeValue(Seconds(interval)));
    client.SetAttribute("PacketSize", UintegerValue(packetSize));

    ApplicationContainer clientApp = client.Install(nodes.Get(0));
    clientApp.Start(Seconds(2.0));
    clientApp.Stop(Seconds(10.0));

    FlowMonitorHelper flowmon;
    Ptr<FlowMonitor> monitor = flowmon.InstallAll();
    Simulator::Stop(Seconds(10.0));
    Simulator::Run();

    monitor->CheckForLostPackets();
    Ptr<Ipv4FlowClassifier> classifier = DynamicCast<Ipv4FlowClassifier>(flowmon.GetClassifier());
    std::map<FlowId, FlowMonitor::FlowStats> stats = monitor->GetFlowStats();

    for (auto &flow : stats) {
        Ipv4FlowClassifier::FiveTuple t = classifier->FindFlow(flow.first);
        std::cout << "Flow " << flow.first << " (Src: " << t.sourceAddress << " -> Dst: " << t.destinationAddress << ")\n";
        std::cout << "  Tx Packets: " << flow.second.txPackets << "\n";
        std::cout << "  Rx Packets: " << flow.second.rxPackets << "\n";
        std::cout << "  Packet Loss: " << (flow.second.txPackets - flow.second.rxPackets) << "\n";
        std::cout << "  Throughput: " << (flow.second.rxBytes * 8.0 / (9 * 1000000)) << " Mbps\n";
        std::cout << "  Mean Delay: " << (flow.second.delaySum.GetSeconds() / flow.second.rxPackets) << " s\n";
        std::cout << "  Jitter: " << (flow.second.jitterSum.GetSeconds() / flow.second.rxPackets) << " s\n";
        std::cout << "  Dropped Packets: " << flow.second.lostPackets << "\n";
    }
    
    Simulator::Destroy();
    return 0;
}
```

### **Running the Simulation**

1. **Compile the Code**:
    
    ```sh
    ./waf
    ```
    
2. **Run the Simulation**:
    
    ```sh
    ./waf --run "scratch/network_performance"
    ```
    
3. **Output Example**:
    
    ```
    Flow 1 (Src: 10.1.1.1 -> Dst: 10.1.1.2)
      Tx Packets: 1000
      Rx Packets: 990
      Packet Loss: 10
      Throughput: 8.8 Mbps
      Mean Delay: 0.0023 s
      Jitter: 0.00012 s
      Dropped Packets: 10
    ```