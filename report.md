
# General description or introduction of the problem and your solution

This homework is very similar to hw01, but it changes the data transfering way from fifo to TLM bus.  

# Implementation details (data structure, flows and algorithms)

The code is very similar to previous hw01.
### main.cpp
```

```
In main.cpp file, most of the code remain unchanged. I change the fifo to initiator for data transfering. 
### testbench.cpp
```

```
Most of the code remains unchanges as hw01. I change fifo to data which will be sented to sobel module to do calculation. Then, I will read data to write new .bmp.

### sobelfilter.cpp
```

```
In SobelFilter.cpp, I changed the mask and the calculation process so that it can apply the mask on red, green, and blue separately, instead of merging those colors to gray in the previous process. I register blocking_transport function for TLMp2p.


# Additional features of your design and models

none

# Experimental results (on different benchmarks and settings)

## origin image
![lena_std_short](https://user-images.githubusercontent.com/76727373/112020617-74aa2000-8b6b-11eb-9897-ef471ba26100.png)
## generated img
![req01](https://user-images.githubusercontent.com/76727373/112020712-8ab7e080-8b6b-11eb-8f3e-42f7223407b5.png)

# Discussions and conclusions.

This homework is quite hard, but it is without a doubet a good practice for us to learn systemC. Besides, we have to write our report in English and submit our homework to GitHub, which are not familiar to me. Anyway, thank you for providing us this challenge.
