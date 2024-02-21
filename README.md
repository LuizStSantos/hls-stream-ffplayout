**Different quality outputs hls**

I recommend a CPU with 8 Cores or more, and 8 GB of ram, if using output from the nvidia video card, use 
```
-c:v h264_nvenc
```

**Output**

``` 
mode : hls
``` 

```
-c:v libx264
-preset fast
-tune zerolatency
-profile:v main
-flags +cgop
-aspect 16:9
-c:a aac
-ar 48000
-filter_complex "[0:v]split=4[v1_out][v2_out][v3_out][v4_out]; [v1_out]scale=w=1920:h=1080[v1_out_final]; [v2_out]scale=w=1280:h=720[v2_out_final]; [v3_out]scale=w=960:h=540[v3_out_final]; [v4_out]scale=w=640:h=360[v4_out_final]; [0:a]asplit=4[a1][a2][a3][a4]"
-map "[v1_out_final]" -map "[a1]" -b:v:0 6500k -maxrate:0 6500k -bufsize:0 6500k -b:a:0 128k
-map "[v2_out_final]" -map "[a2]" -b:v:1 3250k -maxrate:1 3250k -bufsize:1 3250k -b:a:1 96k
-map "[v3_out_final]" -map "[a3]" -b:v:2 2000k -maxrate:2 2000k -bufsize:2 2000k -b:a:2 64k
-map "[v4_out_final]" -map "[a4]" -b:v:3 1150k -maxrate:3 1150k -bufsize:3 1150k -b:a:3 48k
-f hls -hls_time 6 -hls_list_size 10 -hls_flags append_list+delete_segments+omit_endlist
-hls_segment_filename /usr/share/ffplayout/public/live/%v-%d.ts
-master_pl_name stream.m3u8
-var_stream_map "v:0,a:0,name:playlist1080p v:1,a:1,name:playlist720p v:2,a:2,name:playlist540p v:3,a:3,name:playlist360p"
/usr/share/ffplayout/public/live/%v.m3u8
``` 
Here we will have 4 different outputs and a master output

**master**

The master contains all the qualities

```
http://localhost:8787/live/stream.m3u8
```

**1080p**

```
http://localhost:8787/live/playlist1080p.m3u8
```

**720p**

```
http://localhost:8787/live/playlist720p.m3u8
```

**540p**

```
http://localhost:8787/live/playlist540p.m3u8
```

**360p**

```
http://localhost:8787/live/playlist360p.m3u8
```
