# Telly End-User Information

Users don’t want to be spied on. 🕵

Developers want to make sure they’re building and supporting the right features. 🤔

We built Telly to meet the needs of both sides. 🤝

Here’s how we’ve built privacy into every stage:

## Data Minimization
Just because someone *can* collect information doesn’t mean that they *should*. We designed Telly to minimize the amount of data that’s collected to what’s useful for developers’ continued development and improvement of their software.

## Local Protection 🧂

<img src="https://user-images.githubusercontent.com/85053/204273276-7450d239-a6f1-4103-aea5-40f72660e3e4.png" width="200px" alt="salted french fry" />

Basic device information (such as device ID, device hostname, username, and user home path) is salted and hashed directly on your device. Only you have access to the salt.

*I’m hungry for french fries now. What does this actually mean?*

Salting adds a random string of characters to the field. Once this random string has been added, this entire field is hashed. Hashing produces an apparently random string of characters that can be used to track something without knowing what it is. Because we’ve added a salt before hashing, this is practically impossible to reverse, even with known information.

## Aggregation

We only display aggregated data. If there is not sufficient data to aggregate, we may provide our customers with a range of possible values, or we may not display any data. Our goal is that the information displayed cannot be linked to you specifically.

## Privacy-Enhancing Methods
We may utilize certain privacy-enhancing methods to the aggregated metrics. This may include differential privacy or other protocols to ensure anonymity (such as l-diversity or k-anonymity).

## Transparency
You are able to see all of the information that’s being transmitted. Just set TELLY_SHOW_ME to a truthy value like true or 1 and Telly will print what is transmitted. Note that just because something is transmitted does not mean that it’s displayed to our customers. We only display the information that meets our privacy standards, as discussed above.

## Control
You can opt out of the use of Telly at any time. To disable Telly's data collection, you can either:
 * Set the `TELLY_DISABLE` environment variable to a truthy value like `Y` or `1`
 * Create a file named `.telly_disable` in your home directory
 * Use a client library utility like `python3 -m telly disable`

## Benefits to Users
If you’re on this page, it’s probably because you’re utilizing an open source library, component, or software package. The information provided by Telly to the developer of this open source material allows them to continue to build this software out for your benefit - whether this is new features, better compatibility, or simply continued support. 

Interested in using Telly in your own project? [Learn more at our website here!](https://gotelly.io)
