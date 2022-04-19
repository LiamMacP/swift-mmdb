# swift-mmdb

A native swift library for reading MMDB files, which include GeoLite2 files 
for mapping IP addresses to countries.

## What Horror Will This Inflict on My Code?

```swift
    let suspectCountries = Set(["CX", "EC"])
    guard let cc = GeoLite2CountryDatabase( from: someFileUrlWhereTheDatabaseLives) else {
        // do something, you don't have a readable country code database.
    }
    
    if let isoCode = cc.countryCode("199.217.175.1"), suspectCountries.contains( isoCode) {
        // do additional checks for these people that may live on islands named after holidays.
    }
}
```

If you are just testing IP addresses for their country of origin, then
you really only need the `.countryCode` method. If you would like to also
know their continent and have access to localized strings for several 
languages then you will want to use the `.search(address:String)` method.

If you want to know more, then you will need a more complete database than I 
am using and to use the `MMDB` layer instead of the `GeoLite2CountryDatabase`
layer, but it isn't hard. Just feed addresses into its `.search` method.

## Why?

My personal blog keeps getting run over by spam comments which exclusively 
come from two countries. I want to prevent anonymous comments from just those
countries.

Debian has a `geoip-database` package which includes data from 
[MaxMind's GeoLite2 database)](https://dev.maxmind.com/geoip/geolite2-free-geolocation-data)
so that seems a good start. Sadly the only Swift library I found was a wrapper of the
C library, and since I develop under macos and deploy on Debian, I didn't want to wrestle
with the nusiance that is to build.

Since MaxMind publishes [MaxMind DB File Format Specification](https://maxmind.github.io/MaxMind-DB/), 
how hard could it be to just read that directly…  

Survey says: "Two half days with a sleep in between"





