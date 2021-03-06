# To do

- Allow filtering based on name or "group"
- Deduplicate streams
- Follow redirects
- Support parsing a text file with a list of URLs

## added by ak

- When to return from the 'for loop'?
  - To 'return' from a 'for loop' should happen for failure only, not for the first success already, no? See commit 'test every segment = all of them must verify, not only the first' ([523bd126a](https://github.com/drandreaskrueger/iptv-stream-cleaner/commit/523bd126a9169a7b1c5db70e54ba977b1e90743e)); is the same logical error repeated in other places too? 
  - And what if there is only 1 failure entry but 9 good URLs in the same file? Perhaps failing also needs a threshold counter? Or does Kodi fail with the first failed entry already?
  - Think longer about that problem. Sometimes m3u8 playlists simply contain several different resolutions ('#EXT-X-STREAM-INF:BANDWIDTH=...') and if one of them fails, that's not really such a big issue, no? So perhaps another condition could be: At least one success per playlist? Opinions?
- As each entry can be checked independently from all others, multithreading would help. Perhaps a multithreading Queue with a small number of workers (to not start 700 threads in one go); however the original order of entries must survive in the output file.
- A progress percentage; can be done by extending `playlist_items ` with an index "detail", and the `nice_title` printing then begins with that index (same index could then also help for the multithreading results ordering).
- Duplicates: Choose the faster/st answering source?
- Printing config: `--errors-only` to suppress any OK message. Better then do not even show the (name, URL) line for those OK entries. Purpose: A practical logfile for reporting back to IPTV.m3u repos. Example: [kodinerds-iptv](https://github.com/jnk22/kodinerds-iptv/issues/421)

### ideas for an alternative approach
Sometimes an m3u file is perfectly well formed - but Kodi still refuses to play it. That is why the `--blacklist-file` option is introduced ([a03aeb27](https://github.com/drandreaskrueger/iptv-stream-cleaner/commit/a03aeb276479d1c733e1b20b3429395adb79d92b)), to filter them out manually.

To solve that automatically, why not simply try PLAYING each stream? Ideas for an alternative approach:

    ffplay $URL

* catch error --> then Kodi would probably also not be able to play that file, so skip that URL as bad.
* if no error after 3 seconds --> 

    kill -s QUIT $PID
    
to kill the process, and keep that URL as good.

#### Does the URL actually stream something?

Strategy: Begin recording the stream, and if that file has nonzero length, it means something is arriving:

    rm out.ts; ffmpeg -i "$URL" -c copy out.ts
    
in a first process, storing the $PID. Then wait 3-10 seconds, and from a second process

    kill -s QUIT $PID	

then the filesize will reveal everything:

    if not os.path.getsize("out.ts"):
        print("stream bad, skipping.")

Combine that with multithreading, while ordering the results by original index - and this could actually be a simpler, faster approach to reach the same goal, no?

Good idea?
    