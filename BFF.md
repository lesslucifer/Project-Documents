# BFF - Backend for Frontend
BFF service serves as the middle layer behind frontend, ensuring seamless communication with the backend. Its top priority is the flexiblity to easily supports all use cases in development, with low-latency and scalability.

![BFF](https://i.imgur.com/IsQcjtI.png)

## Structure

![BFF structure](https://i.imgur.com/sQctDmp.jpg)

- **Dynamic Endpoints**: Empowering frontend developers to seamlessly integrate their service endpoints directly into BFF without the need for code updates.

- **In-App Caching**: Using in-app caching for almost everything, ensures minimal I/O operations, guaranteeing the lowest possible latency for every request.

- **Customization**: Allow flexible and custom logics that sync and apply automatically, putting minimal load on the system.

- **Scalability**: Employing a lazy-sync strategy, effortlessly shares configurations among instances, ensuring a robust and lightweight operation.

- **Full Monitoring**: Logging and monitoring are the only I/O intensive tasks, they wil be efficiently managed by external services through a lightweight Kafka connection, keeping the system fully monitored and optimized.

## GUI

![BFF UI](https://i.imgur.com/sQctDmp.jpg)

With a descriptive and intuitive frontend, BFF simplifies the configuration of each integrated service's API. Enhance your user experience and bid farewell to integration headaches.

The UI configuration transforms into JSON data, subsequently translated into a logical object on the server via json-make-ts library.

## Metrics:
- Successfully handled integration for over 50 internal services at Binance
- The most utilized internal service in Binance, with nearly 100% user adoption
- Accommodated a daily load of over 1 million requests without encountering any overload
- Maintained a latency of 30ms, with 90% of requests processed in less than 15ms
- Quick and hassle-free setup, with integration time for a new service clocking in at less than 15 minutes.