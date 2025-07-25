<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [Scrumdinger/Views/AnalyticsView.swift](https://github.com/agattani123/swear-jar/blob/main/Scrumdinger/Views/AnalyticsView.swift)

</details>

# Analytics and Reporting

## Introduction

The "Analytics and Reporting" feature in this project provides a user interface and functionality for tracking and analyzing the usage of profane or inappropriate words. It allows users to add or delete words from a profanity set, displays a frequency counter for the tracked words, and shows a progress view indicating the user's overall profanity usage relative to a predefined limit. This feature is likely part of a larger application focused on monitoring and potentially restricting the use of inappropriate language.

## User Interface

The `AnalyticsView` struct represents the main view for the "Analytics and Reporting" feature. It consists of the following UI components:

1. **Progress View:** A visual indicator showing the user's total profanity usage relative to a limit of 50 words.
2. **Text Field:** Allows the user to enter a word to add or delete from the profanity set.
3. **Add and Delete Buttons:** Buttons to add or delete the entered word from the profanity set.
4. **Frequency Counter:** A list displaying the tracked words and their respective frequencies of usage.

Sources: [Scrumdinger/Views/AnalyticsView.swift:18-54]()

## Data Management

The `AnalyticsView` interacts with the Realm database to store and retrieve user-specific data related to profanity tracking. The relevant data models and operations are:

### UserInfo

This is likely a Realm object model representing a user's information, including their profanity set, frequency map, and total profanity score.

```swift
if let user = users.first {
    // Access user's profanitySet, freqMap, and totalScore properties
}
```

Sources: [Scrumdinger/Views/AnalyticsView.swift:89, 107, 137]()

### Profanity Set

A set of strings representing the profane or inappropriate words being tracked for the user.

```swift
try! realm.write {
    user.profanitySet.append(word.lowercased())
    // ...
}
```

Sources: [Scrumdinger/Views/AnalyticsView.swift:99, 137-142]()

### Frequency Map

A dictionary mapping each tracked word (string) to its frequency of usage (integer).

```swift
user.freqMap[word.lowercased()] = 0
```

Sources: [Scrumdinger/Views/AnalyticsView.swift:100, 145]()

### Total Profanity Score

A numeric value representing the user's overall profanity usage, likely calculated as the sum of frequencies for all tracked words.

```swift
totalCount = Double(min(user.totalScore, 50))
```

Sources: [Scrumdinger/Views/AnalyticsView.swift:86]()

## Word Management

The `AnalyticsView` provides functionality to add or delete words from the user's profanity set and update the corresponding frequency map and total profanity score.

### Adding a Word

When the user enters a word and clicks the "Add" button, the following steps are performed:

1. Check if the word is already in the user's profanity set.
2. If the word is not in the set, add it to the profanity set and initialize its frequency in the frequency map to 0.
3. If the word is already in the set, display an error indication.

```swift
func addWord(word: String) {
    if let user = users.first {
        if user.profanitySet.contains(word.lowercased()) {
            incorrectInfo = 2 // Error indication
        } else {
            incorrectInfo = 0 // No error
            try! realm.write {
                user.profanitySet.append(word.lowercased())
                user.freqMap[word.lowercased()] = 0
            }
        }
    }
    self.loadFreq()
}
```

Sources: [Scrumdinger/Views/AnalyticsView.swift:107-122]()

### Deleting a Word

When the user enters a word and clicks the "Delete" button, the following steps are performed:

1. Check if the word is in the user's profanity set.
2. If the word is in the set, remove it from the profanity set and remove its entry from the frequency map.
3. Recalculate the user's total profanity score by summing the frequencies of the remaining words in the frequency map.
4. If the word is not in the set, display an error indication.

```swift
func deleteWord(word: String) {
    if let user = users.first {
        if user.profanitySet.contains(word.lowercased()) {
            incorrectInfo = 0 // No error
            var profanityCopy: [String] = []
            for profanity in user.profanitySet {
                if profanity != word.lowercased() {
                    profanityCopy.append(profanity)
                }
            }
            try! realm.write {
                user.profanitySet.removeAll()
                for copy in profanityCopy {
                    user.profanitySet.append(copy)
                }
                user.freqMap[word.lowercased()] = nil
                user.totalScore = 0
                for key in user.freqMap.keys {
                    user.totalScore += (user.freqMap[key] ?? 0)
                }
            }
        } else {
            incorrectInfo = 2 // Error indication
        }
    }
    self.loadFreq()
}
```

Sources: [Scrumdinger/Views/AnalyticsView.swift:127-158]()

## Frequency Counter

The `AnalyticsView` displays a list of tracked words and their respective frequencies of usage. This list is populated by iterating over the tuples created from the user's frequency map.

```swift
ForEach(0...tuples.count, id: \.self) { idx in
    if (idx != tuples.count) {
        HStack(spacing: 10) {
            Text(tuples[idx].wrds)
            Text("\(tuples[idx].freq)")
        }
    }
}
```

The `tuples` array is created by converting the user's frequency map into an array of `(freq: Int, wrds: String)` tuples, sorted in descending order by frequency.

```swift
func loadFreq() {
    let savedUsername = UserDefaults.standard.object(forKey: "username") as? String ?? ""
    let savedPassword = UserDefaults.standard.object(forKey: "password") as? String ?? ""
    let users = realm.objects(UserInfo.self).where {
        $0.password == savedPassword && $0.username == savedUsername
    }
    if let user = users.first {
        tuples = []
        for key in user.freqMap.keys {
            if let num = user.freqMap[key] {
                tuples.append((num, key))
            }
        }
        totalCount = Double(min(user.totalScore, 50))
    }
    tuples.sort(by: { $0.freq > $1.freq })
}
```

Sources: [Scrumdinger/Views/AnalyticsView.swift:73-87, 55-69]()

## Progress View

The `AnalyticsView` displays a progress view that shows the user's total profanity score relative to a predefined limit of 50 words.

```swift
ProgressView(value: totalCount, total: 50.0).frame(width: 250)
```

The `totalCount` value is calculated as the minimum of the user's `totalScore` and 50, ensuring that the progress view does not exceed the limit.

```swift
totalCount = Double(min(user.totalScore, 50))
```

Sources: [Scrumdinger/Views/AnalyticsView.swift:21, 86]()

## Conclusion

The "Analytics and Reporting" feature provides a user interface and functionality for tracking and analyzing the usage of profane or inappropriate words. It allows users to manage a profanity set, displays a frequency counter for tracked words, and shows a progress view indicating the user's overall profanity usage relative to a predefined limit. This feature is likely part of a larger application focused on monitoring and potentially restricting the use of inappropriate language.