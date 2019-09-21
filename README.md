# movie-scenes-analysis

### Downloading videos for a given author/channel-id
```
youtube-dl -i --get-id https://www.youtube.com/user/MovieclipsTrailers | tee <output>
```
### Downloading videos IDs uploaded after a particular date (YYYYMMDD):
```
youtube-dl -i --dateafter 20180701 --get-id https://www.youtube.com/user/MovieclipsTrailers | tee <output>
```

### Dataset metadata generation
#### Getting statistics viewcount, likecount, dislikecount, comments
```

```
#### Getting Number of frames, resolution 
```
for i in `ls videos/*.mp4`; do 
  ID=`basename $i|sed -e 's/YTID_//g' -e s/\.mp4//g`;
  nframes=`ffmpeg -i $i -map 0:v:0 -c copy -f null - 2>&1|grep "frame="|cut -d' ' -f2`;
  res=`ffprobe -v error -select_streams v:0 -show_entries stream=width,height -of csv=p=0 $i`; 
  echo "$ID",$nframes,$res; 
  done > >(tee moviescenes-metadata.csv)
```
### Getting title 
```
for i in `ls stats/*.json`; do 
  id=`basename $i|sed -e 's/\.json//g' -e 's/TTD_//g'`; 
  ttl=`jq .Title $i`; 
  echo $id,$ttl; 
  done > >(tee moviescenes-titles.csv) 
```
