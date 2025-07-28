<details>
<summary>Relevant source files</summary>

The following file was used as context for generating this wiki page:

- [Scrumdinger/Models/UserInfo.swift](https://github.com/agattani123/swear-jar/blob/main/Scrumdinger/Models/UserInfo.swift)

</details>

# User Data Management

## Introduction

The User Data Management system in the Scrumdinger project is responsible for handling user-related data, including authentication credentials, user identifiers, profanity tracking, and scoring. It utilizes the Realm database to store and manage this information efficiently. The `UserInfo` class serves as the central data model for encapsulating user-specific data.

## User Data Model

The `UserInfo` class, defined in the `UserInfo.swift` file, is a Realm Object subclass that represents the user data model. It contains the following properties:

### Properties

- `username: String`: Stores the user's username for authentication purposes.
- `password: String`: Stores the user's password for authentication purposes.
- `userId: String`: A unique identifier for the user.
- `totalScore: Int`: Keeps track of the user's overall score, likely related to profanity tracking.
- `profanitySet: List<String>`: A Realm `List` that stores the set of profanities associated with the user.
- `freqMap: Map<String, Int>`: A Realm `Map` that likely stores the frequency of occurrences for each profanity.

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

Sources: [Scrumdinger/Models/UserInfo.swift:8-14]()

## User Authentication

The `UserInfo` class stores the user's `username` and `password` properties, which are likely used for authentication purposes within the application. These properties are marked as `@objc dynamic`, indicating that they are dynamically accessible from Objective-C and can be observed for changes.

## User Identification

Each user is assigned a unique `userId` property, which can be used to identify and associate data with a specific user throughout the application.

## Profanity Tracking

The `UserInfo` class includes two properties related to profanity tracking:

1. `profanitySet: List<String>`: This Realm `List` stores the set of profanities associated with the user. It likely contains unique profane words or phrases that the user has used or encountered within the application.

2. `freqMap: Map<String, Int>`: This Realm `Map` likely stores the frequency of occurrences for each profanity in the `profanitySet`. The keys in the `Map` represent the profane words or phrases, and the values represent the corresponding frequency counts.

## Scoring System

The `totalScore` property is an integer that keeps track of the user's overall score. The scoring mechanism is not explicitly defined in the provided code, but it is likely related to the profanity tracking system. For example, the score could be incremented or decremented based on the user's usage of profane language or adherence to profanity-free communication.

Sources: [Scrumdinger/Models/UserInfo.swift]()

## Conclusion

The User Data Management system in the Scrumdinger project revolves around the `UserInfo` class, which encapsulates user-specific data such as authentication credentials, user identifiers, profanity tracking, and scoring. This data model is designed to store and manage user information efficiently using the Realm database. The inclusion of properties like `profanitySet` and `freqMap` suggests that the application has a profanity tracking feature, potentially used for educational or monitoring purposes.