HOW SHAZAM WORKS
----------------

## How the service work

1. Shazam has a database of fingerprinted music (via spectrogram), the
   entire song is fingerprinted.
2. User tags the music by sending a fingerprint of 10 sec music fragment
3. Shazam matches fingerprints in database
4. On match, Shazam will be send info back

The fingerprinting technology is the key here. This is the one that will
create the hashes.

Next, efficient searching the hashes is important.


## Guiding principles for good hashes

* temporarily localized: hash is calculated based on audio samples near
  a corresponding point in time.
* translation-invariant: don't care where in the audio file the time
  fragment will be taken, so the interesting data points need to be present
  in the given time frame all over the file
* robust: hash should also be able to be generated from a degraded copy
  of the audio.
* sufficiently entropic? => something to do with false matches...



## Fingerprinting

Spectrogram => Constellation map => Hashes

### Creation constellation map from spectrogram

Spectrogram contains all data, only the hight intensity points are kept.

Coordinates of these points are mapped as "constellation map" (just the same
time/frequency, but just with almost no points, only the high intensity ones).
The amplitude data itself is not kept (rational: the intensity can vary with
quality, but the fact there is/should be a peak not. So don't care how intense,
only there is a peak).

We could not overlay the map of the 10s sample with that of the song, just
keep sliding the sample and somewhere the points will overlap (the part where
the sample is taken) => we know now the time offset of the sample.

We now have a system in which data points will map even when:
* lots of surplus points are added due to noise
* lots of points are deleted (due to reduced quality?)

### Consequence pairing

If we don't pair we only have 10 bit ints for hashes. Not we have 30 bits
for hashes => hash is 1 million time more specific => much faster

But if F=10, we also have F (10) times the number of hashes => incrementing
storage needs + decreasing speed since more hashes need to be searched
(F*F since F for sample, F for song).

=> speedup factor is 1000000/F^2 => 10 000 (if f=10) over single constellation
   point searches.

But we decrease point survival. Since we need two points to survive that
make up the pair. So from p to p^2 (note 0 <= 1 <=1, so squaring it will
decrease the probability). But at the same time, we have F times more 
hashes => at the end, just a bit less point survival (discover-ability).

End result:
* 10 000 increase in speed
* 10 times as much storage
* small loss in signal detection

### Fast combinatorial hashing

The question now is about: finding the registration offset fast (if none
is found, there is no match).

Just comparing constellation maps (entire song / sample) is just too slow.

To speed up the process do:
1. take anchor points (those can be all or some of the points) (density:
   ratio of total points chosen)
2. define a target area (and size) for each anchor point (the size will
   determine the number of points in the area) (fan-out)
3. pair the anchor with each of the target points and add a time difference
   information

So if one anchor point as a target area with 10 points 10 hashes are 
created (10 pairs).

Each hash contains (32 bit unsigned int)
* 10 bits: frequency anchor
* 10 bits: frequency target
* 10 bits: time offset

This will be augmented with another 32 bit unsigned int (so 64 bit struct)
* time offset beginning of song
* song id

### Relation sound quality / density&fan-out:

Different density and fan-out determine the number of hashes generated.

Relation sound quality / density&fan-out:
* high quality => less density&fan-out
* low quality => high density&fan-out

=> total recordings have less hashes, the sample music more.


## Searching & sorting

For all the sample hashes do:
  - search the db, if match with existing hash do:
      - create a hash/time-offset pair
      - find a track id bin,
        if found:
          - add pair to bin
        if not found:
          - create new bin with track id
          - add pair to bin

Result: we now have different bins (tracks), with in each bin (possible) multiple
hashes and their offset to beginning song. In a sense, this is our "shortlist".

Each bin (track) will be analyzed by plotting the matches on a graph:
* horizontal axis, hash, time offset in song (of anchor point)
* vertical axis, hash, time offset in sample (of anchor point)

=> if the files match, the points should be in a vertical line (as time
increase (song/sample), there should be more matches).

We can now reduce the problem to finding a significant number of points
form a diagonal line within the scatterplot.

### Calculating the diagonal

Now the problem shifts to efficiently calculating this diagonal.

There are many statistical techniques so it's not hard to do. We just need
something very efficient.

So they do:
For each of the matches do:
  - calculate time offset (actual offset beginning song) of the match 
    where t (in song) = t (in sample) + actual offset beginning song
  - find time offset in list,
    if found:
      - increment total nr of occurrences with one
    if not
      - add time offset to list and set occurrence to one

=> we now have the time offset / frequencies => this is the histogram 
   of time offsets.

Then on the histogram we do:
  - sort on frequency (so we can get the histogram peak)
    => the total score of the track is the frequency of the frequency peak ()

=> We now have a score

### Calculate if score is significant

We calculate if the match is significant by:
- create histogram for incorrectly matching tracks (== all other bins)
- probability density function of the highest incorrect matching track is created

=> don't really understand when is significant by takes the following into account:
- the highest incorrectly matches track (bin)
- all the tracks in the db
- an accepted offset

If significant, track info is returned.


## Terminology

### Spectrogram

A spectrogram is a way of fingerprinting music.

[Spectrogram](http://en.wikipedia.org/wiki/Spectrogram): a visual representation of frequencies in a sound.

It encodes three data points:
* time (horizontal axis)
* frequency (vertical axis)
* amplitude of a particular frequency at a specific time (how it changes), encoded as color => intensity

[Amplitude] is the change (delta) of the frequency over a single time period.

### Histogram

[Histogram](http://en.wikipedia.org/wiki/Histogram) is a a graphical representation of the distribution of data.
For this it plots:
* the values (or some ranges of values)
* frequency it occurs (number of times in dataset)


## Questions

* Shazam will only work when the songs are played in constant time (or at
  least, we did communicate to Shazam the time increase/decrease)
  => yes, appears to be the case.


## References

* [An Industrial-Strength Audio Search Algorithm](http://www.ee.columbia.edu/~dpwe/papers/Wang03-shazam.pdf)
* [Creating Shazam with Java](http://www.redcode.nl/blog/2010/06/creating-shazam-in-java)
* [How Shazam works](http://laplacian.wordpress.com/2009/01/10/how-shazam-works)