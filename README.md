As working on machine learning, I desire a dream hash and PRNG. Lemire:[The fastest conventional random number generator that can pass Big Crush?](https://lemire.me/blog/2019/03/19/the-fastest-conventional-random-number-generator-that-can-pass-big-crush/)

t1ha helps my projects a lot, but I am just too thirsty to speed and simplicity ...

The main insight of my hash and PRNG is the following method (inspired by https://github.com/vnmakarov/mum-hash):

uint64_t	mum(uint64_t	A,	uint64_t	B){

	__uint128_t	r=A^p0;	r*=B^p1;
	
	return	(r>>64)^r;
	
}

Only two rounds of such mum operation completely randomized the bits. Thus my hash function become very simple and fast.

The quality is good for wyhash passing SMHasher, BigCrush, practrand. The speed benchmarks are as follow:

https://github.com/rurban/smhasher

| key/cycles | wyhash | XXH3 | t1ha2_atonce | t1ha1_64le | t1ha0_aes_noavx | speedup |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 1-byte | 16.00 | 19.00 | 29.00 | 30.64 | 29.28 | 18.75% |
| 2-byte | 16.00 | 19.00 | 29.00 | 30.67 | 29.00 | 18.75% |
| 3-byte | 17.00 | 19.00 | 29.00 | 30.65 | 29.00 | 11.76% |
| 4-byte | 16.00 | 17.00 | 29.00 | 30.65 | 29.41 | 6.25% |
| 5-byte | 17.00 | 24.00 | 29.00 | 30.69 | 29.33 | 41.18% |
| 6-byte | 17.00 | 24.00 | 29.00 | 30.71 | 29.28 | 41.18% |
| 7-byte | 18.00 | 24.00 | 29.00 | 30.65 | 29.33 | 33.33% |
| 8-byte | 17.00 | 17.00 | 29.00 | 30.32 | 29.00 | 0.00% |
| 9-byte | 17.00 | 25.00 | 32.00 | 30.00 | 32.00 | 47.06% |
| 10-byte | 17.00 | 25.00 | 32.58 | 30.55 | 32.26 | 47.06% |
| 11-byte | 17.00 | 25.00 | 32.52 | 30.00 | 32.00 | 47.06% |
| 12-byte | 17.00 | 25.00 | 32.40 | 26.99 | 32.70 | 47.06% |
| 13-byte | 17.00 | 25.00 | 32.59 | 26.99 | 32.79 | 47.06% |
| 14-byte | 17.15 | 25.00 | 32.53 | 26.99 | 32.70 | 45.77% |
| 15-byte | 17.00 | 25.00 | 32.41 | 26.99 | 32.73 | 47.06% |
| 16-byte | 17.00 | 25.00 | 32.00 | 26.66 | 32.27 | 47.06% |
| 17-byte | 18.00 | 28.00 | 36.80 | 34.00 | 36.00 | 55.56% |
| 18-byte | 18.00 | 28.00 | 36.78 | 34.00 | 36.00 | 55.56% |
| 19-byte | 18.00 | 28.00 | 36.98 | 34.00 | 36.58 | 55.56% |
| 20-byte | 18.00 | 27.00 | 37.03 | 34.00 | 37.25 | 50.00% |
| 21-byte | 18.00 | 27.31 | 37.07 | 34.11 | 37.21 | 51.72% |
| 22-byte | 18.00 | 27.60 | 36.93 | 34.00 | 36.63 | 53.33% |
| 23-byte | 18.00 | 27.51 | 36.71 | 34.00 | 36.00 | 52.83% |
| 24-byte | 18.00 | 27.00 | 36.34 | 34.00 | 36.00 | 50.00% |
| 25-byte | 18.00 | 27.62 | 40.77 | 27.11 | 40.97 | 50.61% |
| 26-byte | 18.00 | 27.79 | 40.51 | 27.00 | 40.97 | 50.00% |
| 27-byte | 18.00 | 27.78 | 40.54 | 27.00 | 40.99 | 50.00% |
| 28-byte | 18.00 | 27.58 | 40.00 | 27.00 | 40.98 | 50.00% |
| 29-byte | 18.00 | 27.57 | 40.72 | 27.00 | 40.87 | 50.00% |
| 30-byte | 18.00 | 27.66 | 40.56 | 27.00 | 40.96 | 50.00% |
| 31-byte | 18.00 | 27.69 | 40.61 | 27.00 | 40.87 | 50.00% |


----------------------------------------
https://github.com/leo-yuriev/t1ha

t1ha2_atonce            :     18.219 cycle/hash,  2.603 cycle/byte,  0.384 byte/cycle,  1.153 GB/s @3.0GHz 

t1ha2_atonce128*        :     34.812 cycle/hash,  4.973 cycle/byte,  0.201 byte/cycle,  0.603 GB/s @3.0GHz 

t1ha2_stream*           :     80.938 cycle/hash, 11.562 cycle/byte,  0.086 byte/cycle,  0.259 GB/s @3.0GHz 

t1ha2_stream128*        :    101.813 cycle/hash, 14.545 cycle/byte,  0.069 byte/cycle,  0.206 GB/s @3.0GHz 

t1ha1_64le              :     19.219 cycle/hash,  2.746 cycle/byte,  0.364 byte/cycle,  1.093 GB/s @3.0GHz 

t1ha0                   :     16.109 cycle/hash,  2.301 cycle/byte,  0.435 byte/cycle,  1.304 GB/s @3.0GHz 

xxhash32                :     18.922 cycle/hash,  2.703 cycle/byte,  0.370 byte/cycle,  1.110 GB/s @3.0GHz 

xxhash64                :     26.219 cycle/hash,  3.746 cycle/byte,  0.267 byte/cycle,  0.801 GB/s @3.0GHz 

StadtX                  :     19.266 cycle/hash,  2.752 cycle/byte,  0.363 byte/cycle,  1.090 GB/s @3.0GHz 

wyhash                  :     16.109 cycle/hash,  2.301 cycle/byte,  0.435 byte/cycle,  1.304 GB/s @3.0GHz 

HighwayHash64_portable  :    493.250 cycle/hash, 70.464 cycle/byte,  0.014 byte/cycle,  0.043 GB/s @3.0GHz 

HighwayHash64_sse41     :     68.375 cycle/hash,  9.768 cycle/byte,  0.102 byte/cycle,  0.307 GB/s @3.0GHz 

HighwayHash64_avx2      :     56.719 cycle/hash,  8.103 cycle/byte,  0.123 byte/cycle,  0.370 GB/s @3.0GHz 



Bench for large keys (16384 bytes):

t1ha2_atonce            :   3547.000 cycle/hash,  0.216 cycle/byte,  4.619 byte/cycle, 13.857 GB/s @3.0GHz 

t1ha2_atonce128*        :   3566.000 cycle/hash,  0.218 cycle/byte,  4.595 byte/cycle, 13.784 GB/s @3.0GHz 

t1ha2_stream*           :   3709.000 cycle/hash,  0.226 cycle/byte,  4.417 byte/cycle, 13.252 GB/s @3.0GHz 

t1ha2_stream128*        :   3721.000 cycle/hash,  0.227 cycle/byte,  4.403 byte/cycle, 13.209 GB/s @3.0GHz 

t1ha1_64le              :   3535.000 cycle/hash,  0.216 cycle/byte,  4.635 byte/cycle, 13.904 GB/s @3.0GHz 

t1ha0                   :   1373.000 cycle/hash,  0.084 cycle/byte, 11.933 byte/cycle, 35.799 GB/s @3.0GHz 

xxhash32                :   8200.000 cycle/hash,  0.500 cycle/byte,  1.998 byte/cycle,  5.994 GB/s @3.0GHz 

xxhash64                :   4118.000 cycle/hash,  0.251 cycle/byte,  3.979 byte/cycle, 11.936 GB/s @3.0GHz 

StadtX                  :   3668.000 cycle/hash,  0.224 cycle/byte,  4.467 byte/cycle, 13.400 GB/s @3.0GHz 

wyhash                  :   3355.000 cycle/hash,  0.205 cycle/byte,  4.883 byte/cycle, 14.650 GB/s @3.0GHz 

HighwayHash64_portable  :  43452.560 cycle/hash,  2.652 cycle/byte,  0.377 byte/cycle,  1.131 GB/s @3.0GHz 

HighwayHash64_sse41     :   6434.000 cycle/hash,  0.393 cycle/byte,  2.546 byte/cycle,  7.639 GB/s @3.0GHz 

HighwayHash64_avx2      :   4539.024 cycle/hash,  0.277 cycle/byte,  3.610 byte/cycle, 10.829 GB/s @3.0GHz 

----------------------------------------

https://github.com/lemire/testingRNG

| PRNG |  cycles per byte |
| ---- | ---- |
| xorshift_k4 | 1.60 |
| xorshift_k5 | 1.77 |
| mersennetwister | 2.41 |
| mitchellmoore | 3.12 |
| widynski | 1.88 |
| xorshift32 | 2.07 |
| pcg32 | 1.72 |
| rand | 5.04 |
| aesdragontamer | 0.71 |
| aesctr | 0.83 |
| lehmer64 | 0.86 |
| xorshift128plus | 0.85 |
| xoroshiro128plus | 0.96 |
| splitmix64 | 0.85 |
| pcg64 | 1.16 |
| xorshift1024star | 1.28 |
| xorshift1024plus | 0.91 |
| wyhash64 | 0.67 |


