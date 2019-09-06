# Flight Engine
Mock flight data delivered simply and quickly without a database.

## Running the App Locally
First, make sure you have [Node.js and npm](https://nodejs.org/en/download/) and [MongoDB](https://docs.mongodb.com/manual/installation/) installed, then install remaining project dependencies by running `npm install`.

After dependencies have been installed, run `npm run dev`, which will perform the following actions:
- Transpile TS into `dist` from `src`
- Start the application
- Watch `src` for changes, and transpile into `dist` again after observed changes
- Watch `dist` for changes and restart the application after observed changes

Once the app has started, try hitting `localhost:3030` (`3030` is the default port unless overridden from `.env`) from a browser.

## Randomization via a Seed Value
In order to keep the app lightweight and eliminate the need for a database, this project uses [_seed randomization_](LINK NEEDED) (credit to @JohnKahn for the amazing idea!). If you don't care about the way data is generated, just read the first two bullets below and skip the rest.

Here are some important things to note if you plan to modify the random data generation:
- After a `Generator` is initialized with a `seed`, the `random` method will generate random data each time it is called, however, this data generation is *deterministic*...
- Because this method of random generation is *deterministic*, the _order_ and _value_ of the "random" value sets generated by multiple calls to `random` for a given seed will _always_ be the same
    - That was a lot... let's say we have `generatorA` and `generatorB` and each have been initialized with a seed value of `RANDOMIZATION_IS_COOL!`. If we call the `random` method of each generator, the result will be _the same_. If, however, we call the `random` method again, the new values will again be the same but they _should_ differ from the first set of values.
- Whenever a `GET /flights` call is performed, the app generates _all_ flights for the specified `date`, _regardless_ of the presence of `origin` and/or `destination`
    - If we only generate a subset of the data based on origin and/or destination, the order in which the flights generated for each O&D pair would differ and flight data would be different depending on the call (e.g., flight `123` retrieved via`/flights?date=2020-01-01` and `/flights?date=2020-01-01&origin=DFW` would differ). Here's an example:
        - `random` method calls 1-10 with a seed of `2020-01-01` will _ALWAYS_ result in: `[1, 7, 9, 1, 8, 4, 5, 7, 2, 3]`
        - `/flights?date=2020-01-01`:
            1. Generate LGA flights (`random` calls 1-4)
            1. Generate MIA flights (`random` calls 6-7)
            1. Generate DFW flights (`random` calls 8-10), flight `123` was call 9 and got a random value of `2`
        - `/flights?date=2020-01-01?origin=DFW`:
            1. Generate DFW flights (`random` calls 1-3), flight `123` was call 2 and got a random value of `7`
        - Because the values are different, the data for flight 123 will not be the same for those two calls


## Testing
This project utilizes framework uses Facebook's [Jest](https://facebook.github.io/jest/) framework for testing. Jest is based on the `Jasmine` framework. While some developers prefer `Mocha`, we've chosen to fully adopt `Jest` on top of `Jasmine` as-is until a significant need requires an alternative solution.

Writing a test is as simple as creating a `*.test.ts` file in the `./src` directory along with an associated `describe()` and `test()` function.

Simply run `npm run test` to run tests.

Additional testing scripts:
- `test`: runs all tests
- `test:changed`: runs tests related to uncommited git changes only

## Contributing
Interested in contributing to the project? Check out our [Contributing Guidelines](.github/CONTRIBUTING.md).