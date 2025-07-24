<details>
<summary>Relevant source files</summary>

The following file was used as context for generating this wiki page:

- [Scrumdinger/Models/UserInfo.swift](https://github.com/agattani123/swear-jar/blob/main/Scrumdinger/Models/UserInfo.swift)

</details>

# Data Storage and Management

## Introduction

The "Data Storage and Management" component within the Scrumdinger project is responsible for handling user-related data, including authentication credentials, profanity tracking, and scoring. It utilizes the Realm database to store and manage this information efficiently. The primary class involved in this process is `UserInfo`, which serves as a model for representing user data within the application.

## UserInfo Model

The `UserInfo` class is a Realm Object that defines the structure and properties for storing user-related data. It consists of the following properties:

### Properties

- `username`: A string property that stores the user's username.
- `password`: A string property that stores the user's password.
- `userId`: A string property that stores a unique identifier for the user.
- `totalScore`: An integer property that keeps track of the user's overall score.
- `profanitySet`: A list of strings that stores the profanities uttered by the user.
- `freqMap`: A map that stores the frequency of each profanity uttered by the user, with the profanity as the key and the frequency as the value.

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

Sources: [Scrumdinger/Models/UserInfo.swift:9-15]()

## Data Flow

The `UserInfo` model is likely used throughout the application to store and retrieve user-related data. The data flow might involve the following steps:

1. User authentication: When a user logs in or creates an account, their `username` and `password` properties are likely validated and stored in the Realm database.
2. User identification: The `userId` property is likely assigned a unique value to identify the user within the application.
3. Profanity tracking: As the user interacts with the application, any profanities uttered are added to the `profanitySet` list, and their frequencies are updated in the `freqMap`.
4. Score management: The `totalScore` property is likely updated based on the user's actions or profanity usage, providing a gamification aspect to the application.

## Realm Integration

The `UserInfo` class inherits from `Object`, which is part of the RealmSwift framework. This integration allows the application to leverage Realm's efficient and lightweight database for storing and querying user data. Realm provides a straightforward way to persist and retrieve objects, making it suitable for managing user-related data within the Scrumdinger application.

Sources: [Scrumdinger/Models/UserInfo.swift:7]()

## Conclusion

The "Data Storage and Management" component in the Scrumdinger project plays a crucial role in handling user-related data, including authentication, profanity tracking, and scoring. The `UserInfo` model, backed by the Realm database, provides a structured way to store and manage this information efficiently. By leveraging Realm's capabilities, the application can seamlessly persist and retrieve user data, enabling features such as user authentication, profanity monitoring, and gamification.