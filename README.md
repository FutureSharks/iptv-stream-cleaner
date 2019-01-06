# IPTV Stream cleaner

Have you tried to download IPTV playlists only to find that a majority of the streams don't work? It's super annoying.

Here is a simply python script to filter out bad or non-functioning video streams from IPTV playlist files. It will check each playlist item, its video stream links and any nested playlist items.

It can also write a new playlist file containing the good streams.

Example:

```console
./stream-cleaner.py --input-file my-iptv-playlist-file.m3u --output-file filtered-playlist.m3u
AF: KTV PLUS | https://svs.itworkscdn.net/ktvpluslive/kplus.smil/playlist.m3u8
  OK playlist data
AF: ANN | http://ns8.indexforce.com:1935/ann/ann/playlist.m3u8
  OK playlist data
DE: MEDICAL | http://egyman.net:1978/live/180118/180118/6727.m3u8
  ERROR playlist data
UK | 5 USA | http://vip.groupiptv.com:7000/live/janna1/janna1/3480.ts
  ERROR video data
AF: AL HADATH | http://starmena.ercdn.net/libya-alhadath/libya-alhadath.m3u8
  Loading playlist: http://starmena.ercdn.net/libya-alhadath/libya-alhadath.m3u8
    Loading playlist: http://starmena.ercdn.net/libya-alhadath/libya-alhadath_480p.m3u8
  Loading video: http://starmena.ercdn.net/libya-alhadath/libya-alhadath_480p-1546614961.ts
    OK loading video data
  OK playlist data
UK | 5 Star | http://vip.groupiptv.com:7000/live/janna1/janna1/3479.ts
  ERROR video data
```

It's perfect for use with [siptv.app](https://siptv.app) or similar apps.
