# movie-scenes-analysis

### Dataset metadata generation

1. After videos are downloaded
```
for i in `ls videos/*.mp4`; do 
  ID=`basename $i|sed -e 's/YTID_//g' -e s/\.mp4//g`;
  nframes=`ffmpeg -i $i -map 0:v:0 -c copy -f null - 2>&1|grep "frame="|cut -d' ' -f2`;
  res=`ffprobe -v error -select_streams v:0 -show_entries stream=width,height -of csv=p=0 $i`; 
  echo "$ID",$nframes,$res; 
  done > >(tee moviescenes-metadata.csv)
```
