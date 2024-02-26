---
author: emwilson
comments: true
date: 2009-02-28 20:28:56+00:00
layout: post
link: https://www.alwaysgetbetter.com/2009/02/28/implementing-lazy-load-proxy-class/
slug: implementing-lazy-load-proxy-class
title: Implementing Lazy Load Using a Proxy Class
wordpress_id: 183
categories:
- C#
- Design Patterns
tags:
- C#
- data types
- Design Patterns
- efficiency
photo:
  source: http://www.flickr.com/photos/76045572@N00/3258171732/
  url: http://farm4.static.flickr.com/3096/3258171732_38e877e923_m.jpg
  description: Relaxing in Slippers
  cc_author: Gog Llundain
  cc_link: http://www.flickr.com/photos/76045572@N00/3258171732/
---

Lazy load is a design pattern wherein an object is not instantiated until the last possible minute. This is very handy when working with lists of items whose contents are expensive to retrieve from the data store.

There are typically three ways of implementing lazy load:
1. Lazy Initialization - The object is set to _null_ and checked/loaded when data is needed
2. Proxy - A proxy class for the object is created using the same interface as the original class; whenever a property is called, the proxy creates the object and returns the correct data
3. Ghost - The class loads only a partial set of its information until more is needed


## Example Situation


In my example situation, we handling a catalogue of artists owned by a fictional record label. For each artist, we will store a name, musical genre, web site address, and number of albums.

[caption id="attachment_184" align="aligncenter" width="142" caption="UML for Artist Class"]![UML for Artist Class](/images/2009/02/artist_class.png)[/caption]

Let's pretend we have thousands of artists on our roster, and we need to print a catalogue containing all of their information. Rather than loading all of that data into memory right away and having to wait until that process is done before we can begin printing, it makes more sense to get a list of how many artists will be printed (so our software knows how many pages to print) but to only load the actual information when we are ready to print it.

The solution is to create an ArtistProxy class. ArtistProxy has an _artist variable who is set to _null_ when it is initialized. Whenever we try to access the artist's name, web site, etc from ArtistProxy, the class creates an Artist (only if not already done) and returns the property from _artist.

Our print function is never aware of ArtistProxy - as far as it is concerned it only ever deals with Artist. We accomplish this by creating an interface - IArtist - which acts as a contract for both ArtistProxy and Artist. If we add more properties to Artist later on, IArtist will keep us honest by forcing us to also update ArtistProxy.

[caption id="attachment_186" align="aligncenter" width="479" caption="Artist, ArtistProxy, and IArtist UML Diagram"]![Artist, ArtistProxy, and IArtist UML Diagram](/images/2009/02/artist_lazyload.png)[/caption]

Now that we understand how our classes relate to each other, it's time to use them in code:

```csharp
// Our printArtists() function looks something like this.
// Notice how we are unaware whether the artist is
// an actual object, or a whether it is a proxy.
public function printArtists( IArtist [] artistList )
{
  foreach ( IArtist artist in artistList )
  {
    printOneArtist( artist );
  }
}

// Implementation of the IArtist interface
public interface IArtist
{
  string Name { get; set; }
  string Genre { get; set; }
  string Website { get; set; }
  int getNumberOfAlbums();
}

// Implementation of Artist class
public class Artist : IArtist
{
  private int _id;
  private string _name;
  private string _genre;
  private string _website;

  public Artist( id )
  {
    _id = id;
  }

  public string Name
  {
    get { return _name; }
    set { _name = value; }
  }

  public string Genre
  {
    get { return _genre; }
    set { _genre = value; }
  }

  public string Website
  {
    get { return _website; }
    set { _website = value; }
  }

  public int getNumberOfAlbums()
  {
    return fictionalDataConnection->getNumberOfAlbums( _id );
  }
}

// Implementation of ArtistProxy class
public class ArtistProxy : IArtist
{
  private Artist _artist;
  private int _id;

  public ArtistProxy( id )
  {
    _artist = null;
    _id = id;
  }

  public string Name
  {
    get
    {
      if ( null == _artist ) _artist = new Artist( _id );
      return _artist.Name;
    }
    set
    {
      if ( null == _artist ) _artist = new Artist( _id );
      _artist.Name = value;
    }
  }

  public string Genre
  {
    get
    {
      if ( null == _artist ) _artist = new Artist( _id );
      return _artist.Genre;
    }
    set
    {
      if ( null == _artist ) _artist = new Artist( _id );
      _artist.Genre = value;
    }
  }

  public string Website
  {
    get
    {
      if ( null == _artist ) _artist = new Artist( _id );
      return _artist.Website;
    }
    set
    {
      if ( null == _artist ) _artist = new Artist( _id );
      _artist.Website = value;
    }
  }

  public int getNumberOfAlbums()
  {
    if ( null == _artist ) _artist = new Artist( _id );
    return _artist.getNumberOfAlbums();
  }
}
```

Of course for the sake of convenience a few things are missing from my example:
1. An actual data source
2. Delegates (presumably one would include a 'LoadArtist' delegate so the proxy will be able to pass the actual loading of its artist to the data layer)
