# Swift MMDB

A native swift library for reading MMDB files, which include GeoLite2 files 
for mapping IP addresses to countries or cities.

## How can you use this?

```swift
    let suspectCountries = Set(["CX", "EC"])
    guard let cc = GeoLite2CountryDatabase( from: someFileUrlWhereTheDatabaseLives) else {
        // do something, you don't have a readable country code database.
    }
    
    if let isoCode = cc.countryCode("199.217.175.1"), suspectCountries.contains(isoCode) {
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

## What does it provide?

- Once your MMDB constructor returns the database, there will be no I/O 
  done. No disk reading (unless you are swapping) and no network access.
- No amount of corrupted database file will allow it to access outside
  its own bytes. (I reengineered away from 'unsafe' to guarantee this.)
- It is possible to create a database file which will recurse beyond any 
  stack you might have. I do not have a limiter. Use a database you trust.
- IP lookups take 200-300µS on the original M1 Mac Mini. That's fast enough
  for me, for now. 

## Building

- Clone the repository
- Run the tests. If it complains about not having a test-data file, then go to your
  checked out copy and `git submodule update --init --recursive` since Xcode and 
  `swift build` probably didn't do that for you. You only need to do that once after checkout.
  If your `Tests/MMDBTests/MaxMind-DB` directory is empty, then you need to do this.
  
### Database Files

> You will need to pay attention to licensing restrictions with the
> associated database you use.

You will need to 'bring-your-own-database' due to licensing concerns. A 
commonly used database is the MaxMind GeoLite2 that can be downloaded by 
creating a developer account. (You can then also use scripts to regularly 
pull a fresh copy.) Other providers, mostly commercial also provide database 
formats that comply with the MMDB specification.

## How feature-complete is this?

This version "passes" all of the MaxMind "good" tests. It doesn't explode on any of the
"bad-data" tests for corrupted databases, but many of those are corrupted in more than
one way and this code bails out reading the metadata so we don't actually test what was
intended.

## What could go wrong?

- There are failure tests currently commented out due to failing from the move from
  full file load to memory mapping. It is planned to remediate this.
- It is possible to create a database file which will recurse beyond any 
  stack you might have. I do not have a limiter. Use a database you trust.
- It is possible to generate an arithmetic exception from a corrupt database.
- Some data types, (dataCacheContainer, endMarker), are
  not implemented and will give you a `fatalError`. I need to find a database which
  uses them to test them.

## Why was this forked?

The original code provides a solid foundation to load and the database without performing
additional I/O loading. Whilst this works within most situations, loading an entire
database can be very memory intensive. On mobile devices, this can result in the application
being killed by the OS.

The [database specification](https://maxmind.github.io/MaxMind-DB/) itself is publicly 
available and providing a database is not corrupted and complies, can be searched in place
through memory mapping. 

This allows for the devices with limited memory to used a paged approach and binary search 
to find appropriate results without loading the entire file.

## License

The original work was completed by [Jim Studt](https://github.com/jimstudt/swift-mmdb) 
under the MIT license. I intend to keep working within this License's constraints whilst
building on the prior great work.
