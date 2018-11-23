
## Architecture

My solution uses MVVM with Rx and a coordinator to handle the (in this case trivial) flows. There is also a ComicsProvider that serves and persists the comics.

## Design

Comics are downloaded in two steps, first the metadata and then the images, as dictated by the API. Metadata is downloaded in batches using the Marvel API, images are downloaded individually from a third party using URLs provided in the metadata.

When a comic is requested (by the scrolling list view) its batch is downloaded, along with the next batch. If a comic is requested we check first if it already has been downloaded, and if not if it has been requested but not received. In either case the next batch will still be downloaded, as needed.

Metadata is saved locally as it is downloaded, and images are later saved into the same database objects.

### Simplifications used

We only attempt to download the images right after the metadata is fetched, so if the app is closed right after downloading the metadata the corresponding images will never be downloaded. In reality the app would periodically (or based on requests for comics) make new attempts to download the images.

Images take lots of space, whereas metadata is almost free, but no attempt is made to prune unused image data. Images also aren't scaled down to the thumbnail size to speed up the list view. In an ideal scenario we would probably make small thumbnails that are kept and prune the full scale iamges.

The preemptive downloading is quite rudimentary, and works best if the list is scrolled in ascending order.

### Frameworks and technologies

The app uses:

- Alamofire for networking
- RxSwift for rx
- Realm for persisting. 
- Swift decodable to deserialise.
- CryptoSwift for signing hashes
- Storyboard (but not segues) for the UIs

Testability:

- The ComicsProvider conforms to a very simple protocol so it can easily be mocked
- ViewModels conform to a generic protocol that makes them easily testable
- Other parts, like the Signer, can easily be converted in a similar manner
- We strive for separation of concerns and limiting API surface area
