# The Vision #

Cycling can be a great way to get around a city, but it's often difficult to find good routes that feel safe. We believe that more people would bike if they had access to better bike directions.

Existing bicycle routing technologies can be great, but they have serious drawbacks:

 - No standard ranking system for streets -- riders don't know what to expect from the algorithm
 - No transparency of tradeoffs between speed, ease of memorization, and stressfulness -- riders don't know whether a given route will feel safe or not.
 - No personalization based on stress tolerance -- riders can't provide their parameters to find a low-stress ride

We aim to develop a new routing engine specifically for city cycling that solves these problems and provides the following advantages:

 - Transparency: street ranking & classification algorithms will be open source and based on peer-reviewed academic work
 - Personalization: allow users to specify their priorities & stress tolerance
 - Continuous improvement: by incorporating user feedback on the quality of their rides, our algorithms will get better with time.
 
Ultimately, we believe that, with this vision, we can produce dramatically better bike directions that will give more people the confidence to ride a bike in their city.

# Existing tools / prior art / similar work #

 - (Rudimentary LTS labeling)[https://github.com/conveyal/r5/blob/master/src/main/java/com/conveyal/r5/labeling/LevelOfTrafficStressLabeler.java] in (Conveyal r5)[https://github.com/conveyal/r5]. ((more info)[http://conveyal.com/blog/2015/12/15/bike-accessibility])
 - 

# The Plan #

## Step 1: Use OSM data to compute "level of traffic stress" ratings for every street in a city ##

In city bicycle planning, it's now commonly accepted that riders (& potential riders) make transportation choices that are informed by the stress they feel during a trip. It's also common to group riders into different levels of stress tolerance: for example, a parent with a small child may have very low tolerance for stress, while a daredevil teenager in lycra may have a much higher tolerance.

The street-classification method called [level of traffic stress](http://transweb.sjsu.edu/project/1005.html) (LTS) uses attributes of the infrastructure (width of bike lane, speed limit, etc) to assign a stress level 1-4 to the street corresponding to the rider group that would be comfortable on that street. Level 1 is the lowest stress; 4 is the highest stress. The original paper contains the "magic numbers" that describe exactly how to obtain the LTS for a street, given perfect data.

A small number of cities have produced LTS maps for their bike networks, often at great cost in terms of data collection and classification effort. These maps are amazing, but they can age quickly when infrastructure or traffic patterns change. In many locations, OpenStreetMap contains enough data to make an educated guess at the LTS for a street, and by automating the process, we can respond to updated infrastructure as soon as it's updated in OSM.

The deliverable for this step is a well-documented algorithm that transforms OSM data into an LTS value and a visualization that shows the results and explains how we came to that LTS value.

### Challenges ###

 - Communicating uncertainty in the face of incomplete data -- how do we measure our confidence in the LTS value when OSM is missing some required data?

## Step 2: Routing profiles informed by LTS analysis ##

With an LTS map in hand, it should be straightforward to produce an OSRM profile for each LTS level. These profiles can provide bike directions for each group of riders, ensuring that the given directions do not exceed the rider's acceptable stress level.

The deliverable for this step is a simple web map that shows computed directions for each LTS level.

### Challenges ###

 - Low-LTS levels will often be disconnected networks. It's not clear how we should handle this: by refusing to route, by allowing walking of bikes (assuming existence of a sidewalk), etc?
 
## Step 3: Correlate street attributes & LTS value with Ride Report stress ratings where available ##

(Ride Report)[https://ride.report] users provide ratings for their bike trips. In cities with sufficient trip density, we can use Ride Report's open data to further inform routing or to check the appropriateness of our LTS guesses.
 
## Future enhancement ##

### More flexible routing ###

Real riders aren't likely to neatly fall into any one of the 4 LTS buckets. For example, an experienced rider may accept a short distance of high stress riding if it saves significant time, or if the alternative is walking on a sidewalk. We could accomodate this need by creating a few more profiles, producing several alternatives in OSRM, and providing more choices to users.

### External data sources ###

A key piece of data in a proper LTS analysis is auto traffic volume. This data isn't in OSM, but some cities have recent "AADT" available for many streets. Sometimes there's even real-time feeds for major arterials. Let's integrate this data in select cities and/or find a standard source that spans many areas.

### Gather more data ###

It may be possible to improve the OSM data in some areas. City governments may contribute data or data entry effort because  they are interested in the LTS analysis. Riders and mappers may contribute data or time because they're interested in better bike directions.
