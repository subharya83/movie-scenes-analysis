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
for id in `cut -c1-11  metadata/moviescenes-metadata-Titles.tsv`; do 
  x=`grep -e "$id" metadata/moviescenes-metadata-Statistics.tsv|sed 's/\t/,/g'`; 
  if [ -z "$x" ]; then 
    x="$id,,,"; 
  fi; 
  echo $x;
done > >(tee rel-Titles-statistics.csv
```
#### Getting Number of frames, resolution 
```
for i in `ls videos/*.mp4`; do 
  ID=`basename $i|sed -e 's/YTID_//g' -e s/\.mp4//g`;
  nframes=`ffmpeg -i $i -map 0:v:0 -c copy -f null - 2>&1|grep "frame="|cut -d' ' -f2`;
  if [ -z $nframes ]; then
    nframes=`ffmpeg -i $i -vcodec copy -f rawvideo -y /dev/null 2>&1|tr ^M '\n'|awk '/^frame=/ {print $2}'|tail -n 1`
  fi 
  
  res=`ffprobe -v error -select_streams v:0 -show_entries stream=width,height -of csv=p=0 $i`; 
  echo "$ID",$nframes,$res; 
done > >(tee moviescenes-metadata.csv)
```
#### Getting YouTube video title 
```
for i in `ls stats/*.json`; do 
  id=`basename $i|sed -e 's/\.json//g' -e 's/TTD_//g'`; 
  ttl=`jq .Title $i`; 
  echo $id,$ttl; 
done > >(tee moviescenes-titles.csv) 
```

### Extracting Year of Movie from YouTube video title string
```
f=metadata/titles.tsv;
nlines=`cat $f|wc -l`; 
for i in `seq 1 $nlines`; do 
  ln=`awk "NR==$i" $f`; 
  year=`echo $ln|sed -n 's/.*\([1-9][0-9][0-9][0-9]\).*/\1/p'`; 
  echo $year;  
done > >(tee metadata/titles-years.csv)
```


