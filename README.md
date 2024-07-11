# Swift MMDB

A native swift library for reading MMDB files, which include GeoLite2 files 
for mapping IP addresses to countries or cities, within memory constrained 
environments.

## What does it provide?

- Once your MMDB constructor returns the database. The whole file is not 
  read into memory, but parsed just in time as required. There are no 
  network requests outputted from this library.
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

> Most of the bad-data tests are commented out due to migrating to memory mapping read.
> It is planned to remediate this in an upcoming change.

This version "passes" all of the MaxMind "good" tests.

## What could go wrong?

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
