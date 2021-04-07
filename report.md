
# General description or introduction of the problem and your solution

This homework is very similar to hw01, but it changes the data transfering way from fifo to TLM bus.  

# Implementation details (data structure, flows and algorithms)

The code is very similar to previous hw01.
### main.cpp
```

Testbench tb("tb");
SimpleBus<1, 1> bus("bus");
bus.set_clock_period(sc_time(CLOCK_PERIOD, SC_NS));
SobelFilter sobel_filter("sobel_filter");
tb.initiator.i_skt(bus.t_skt[0]);
bus.setDecode(0, SOBEL_MM_BASE, SOBEL_MM_BASE + SOBEL_MM_SIZE - 1); //Inheritance from memorymap
bus.i_skt[0](sobel_filter.t_skt);

```
In main.cpp file, we have to initialize bus interface. We bind initiator in testbench to target socket of bus and bind initiator socket to target socket of sobelfilter. Therefore, testbench can tesbench can transfer data to soblefilter via bus and vise versa. bus.setDecode is to set memory address for them to read/ write data.

### SimpleBus.h
```
SimpleBus(sc_core::sc_module_name name, double clock_period_in_ps = 1000,
          bool trace = false, bool masked = true)
    : sc_core::sc_module(name), MemoryMap(name, NR_OF_INITIATOR_SOCKETS),
      clock_period(clock_period_in_ps, sc_core::SC_PS), m_trace(trace),
      m_is_address_masked(masked) {
  for (unsigned int i = 0; i < NR_OF_TARGET_SOCKETS; ++i) {
    t_skt[i].register_b_transport(this, &SimpleBus::initiatorBTransport, i); // define SocketId as i
    t_skt[i].register_transport_dbg(this, &SimpleBus::transportDebug, i);
    t_skt[i].register_get_direct_mem_ptr(this, &SimpleBus::getDMIPointer, i);
  }
  for (unsigned int i = 0; i < NR_OF_INITIATOR_SOCKETS; ++i) {
    i_skt[i].register_invalidate_direct_mem_ptr(
        this, &SimpleBus::invalidateDMIPointers, i);
  }
}
```

This is the constructor of bus. We want to bind our b_transport and other function to bus, so whenever we call b_transport it will automatically call our b_transport. 

```
unsigned int transportDebug(int SocketId, transaction_type &trans) {
  Addr orig = trans.get_address();
  Addr offset;
  int portId = getPortId(orig, offset);

  if (portId < 0) {
    std::cout << "ERROR: " << name() << ": transportDebug()"
              << ": Invalid (undefine memory mapped) address == "
              << tshsu::print(trans.get_address()) << std::endl;
    assert(false);
  }

  if (m_trace) {
    printf("TLM: %s dbg decode:0x%llX -> i_skt[%d]\n", name(), orig, portId);
  }
  // It is a static port ?
  initiator_socket_type *decodeSocket = &i_skt[portId];
  if (m_is_address_masked) {
    trans.set_address(offset);
  }
  return (*decodeSocket)->transport_dbg(trans);
}

```
This is the b_transport functino we use in my program. First it will get corresponding portId from memorymap. Then, do error detection to avoid portId < 0. When we have correct portId we can get initiator and transfer data to next socket.

### testbench.cpp

Most of the code remains unchanges as hw01 and hw03.

### sobelfilter.cpp

Most of the code remains unchanges as hw01 and hw03.


# Additional features of your design and models

none

# Experimental results (on different benchmarks and settings)

## origin image
![lena_std_short](https://user-images.githubusercontent.com/76727373/112020617-74aa2000-8b6b-11eb-9897-ef471ba26100.png)
## generated img
![req01](https://user-images.githubusercontent.com/76727373/112020712-8ab7e080-8b6b-11eb-8f3e-42f7223407b5.png)

# Discussions and conclusions.

This homework is quite hard, but it is without a doubet a good practice for us to learn systemC. Besides, we have to write our report in English and submit our homework to GitHub, which are not familiar to me. Anyway, thank you for providing us this challenge.
