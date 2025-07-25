<details>
<summary>Relevant source files</summary>

The following file was used as context for generating this wiki page:

- [Scrumdinger/Models/UserInfo.swift](https://github.com/agattani123/swear-jar/blob/main/Scrumdinger/Models/UserInfo.swift)

</details>

# Data Storage and Management

## Introduction

The "Data Storage and Management" component within the Scrumdinger project is responsible for handling user-related data, including user credentials, profanity tracking, and scoring information. It utilizes the Realm database to persist and manage this data efficiently. The primary data model is the `UserInfo` class, which encapsulates various properties and collections related to user information.

## Data Model

The `UserInfo` class is a Realm Object that serves as the central data model for storing and managing user-related information. It consists of the following properties and collections:

### Properties

- `username`: A string property that stores the user's username.
- `password`: A string property that stores the user's password.
- `userId`: A string property that stores a unique identifier for the user.
- `totalScore`: An integer property that keeps track of the user's overall score.

### Collections

- `profanitySet`: A list of strings that stores the profanity words used by the user.
- `freqMap`: A map that stores the frequency of each profanity word used by the user, with the key being the profanity word and the value being the frequency count.

```swift
class UserInfo: Object {
    @objc dynamic var username: String = ""
    @objc dynamic var password: String = ""
    @objc dynamic var userId: String = ""
    @objc dynamic var totalScore: Int = 0
    dynamic var profanitySet: List<String> = List<String>()
    dynamic var freqMap: Map<String, Int> = Map()
}
```

Sources: [Scrumdinger/Models/UserInfo.swift:8-15]()

## Realm Integration

The `UserInfo` class is designed to be used with the Realm database, which is a popular mobile database solution. Realm provides a seamless integration with Swift, allowing for efficient data storage and retrieval.

The `@objc` and `dynamic` keywords are used to mark the properties as Realm-managed properties, ensuring that changes to these properties are automatically tracked and persisted by the Realm database.

The `profanitySet` and `freqMap` properties are defined as `List<String>` and `Map<String, Int>`, respectively. These are Realm-specific collection types that provide efficient storage and querying capabilities for lists and dictionaries.

## Data Flow

The data flow within the "Data Storage and Management" component likely involves the following steps:

1. User authentication: When a user logs in, their username and password are verified against the stored `UserInfo` objects in the Realm database.
2. User data retrieval: After successful authentication, the user's data, including their profanity set, frequency map, and total score, is retrieved from the Realm database and loaded into the application.
3. Profanity tracking: As the user interacts with the application and uses profanity words, these words are added to the `profanitySet` collection, and their frequencies are updated in the `freqMap`.
4. Score calculation: Based on the profanity usage and potentially other factors, the user's `totalScore` is calculated and updated in the Realm database.
5. Data persistence: Whenever changes are made to the user's data (e.g., adding new profanity words, updating frequencies, or modifying the total score), these changes are automatically persisted in the Realm database by the Realm framework.

## Potential Improvements

Based on the provided source file, here are some potential improvements or considerations for the "Data Storage and Management" component:

1. **Data Validation**: Implement validation rules for user input, such as enforcing password complexity requirements or ensuring that usernames are unique.
2. **Data Encryption**: Consider encrypting sensitive user data, like passwords, before storing them in the Realm database to enhance security.
3. **Data Relationships**: Explore the possibility of introducing relationships between different Realm objects to model more complex data structures, if needed.
4. **Data Synchronization**: If the application requires data synchronization across multiple devices or with a server, investigate Realm's synchronization capabilities or integrate with a backend service.
5. **Data Backup and Restore**: Implement mechanisms for backing up and restoring user data to ensure data integrity and enable user data migration between devices or app versions.

Sources: [Scrumdinger/Models/UserInfo.swift]()

## Conclusion

The "Data Storage and Management" component in the Scrumdinger project plays a crucial role in managing user-related data, including user credentials, profanity tracking, and scoring information. By leveraging the Realm database, the application can efficiently store and retrieve this data, ensuring data persistence and enabling seamless user experiences across sessions and devices.