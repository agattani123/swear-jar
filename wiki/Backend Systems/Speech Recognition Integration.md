<details>
<summary>Relevant source files</summary>

The following files were used as context for generating this wiki page:

- [Scrumdinger/Models/SpeechRecognizer.swift](https://github.com/agattani123/swear-jar/blob/main/Scrumdinger/Models/SpeechRecognizer.swift)

</details>

# Speech Recognition Integration

## Introduction

The "Speech Recognition Integration" feature in this project provides functionality for real-time speech-to-text transcription and profanity detection. It utilizes the `SFSpeechRecognizer` and `AVAudioEngine` classes from the Speech and AVFoundation frameworks, respectively. This feature is implemented in the `SpeechRecognizer` class, which acts as a helper for transcribing speech input and counting profane words.

The `SpeechRecognizer` class is designed as an `ObservableObject` and an `actor`, allowing it to be used in SwiftUI views and ensuring thread-safe access to its properties and methods. It interacts with the Realm database to store user information, including profanity counts and word frequencies.

Sources: [Scrumdinger/Models/SpeechRecognizer.swift]()

## Initialization and Setup

### SpeechRecognizer Initialization

The `SpeechRecognizer` class is initialized with an instance of `SFSpeechRecognizer`. It also retrieves the user's information from the Realm database, including their username, password, profanity set, and previous score.

```swift
init() {
    recognizer = SFSpeechRecognizer()
    // ... (retrieve user information from Realm)
}
```

Sources: [Scrumdinger/Models/SpeechRecognizer.swift:40-59]()

### Requesting Permissions

During initialization, the `SpeechRecognizer` class requests permission to access the speech recognizer and the microphone. This is done asynchronously using the `SFSpeechRecognizer.hasAuthorizationToRecognize()` and `AVAudioSession.hasPermissionToRecord()` methods.

```swift
Task {
    do {
        guard await SFSpeechRecognizer.hasAuthorizationToRecognize() else {
            throw RecognizerError.notAuthorizedToRecognize
        }
        guard await AVAudioSession.sharedInstance().hasPermissionToRecord() else {
            throw RecognizerError.notPermittedToRecord
        }
    } catch {
        transcribe(error)
    }
}
```

Sources: [Scrumdinger/Models/SpeechRecognizer.swift:60-70](), [Scrumdinger/Models/SpeechRecognizer.swift:201-211]()

## Speech Recognition Process

### Transcription Setup

The `transcribe()` method is responsible for setting up the speech recognition process. It creates an `AVAudioEngine` instance and a `SFSpeechAudioBufferRecognitionRequest` object, which is used to handle the recognition task.

```swift
private func transcribe() {
    guard let recognizer, recognizer.isAvailable else {
        self.transcribe(RecognizerError.recognizerIsUnavailable)
        return
    }

    do {
        let (audioEngine, request) = try Self.prepareEngine()
        self.audioEngine = audioEngine
        self.request = request
        self.task = recognizer.recognitionTask(with: request, resultHandler: { [weak self] result, error in
            self?.recognitionHandler(audioEngine: audioEngine, result: result, error: error)
        })
    } catch {
        self.reset()
        self.transcribe(error)
    }
}
```

Sources: [Scrumdinger/Models/SpeechRecognizer.swift:84-102]()

The `prepareEngine()` method sets up the audio engine and the recognition request, configuring the audio session and installing a tap on the input node to capture audio data.

```swift
private static func prepareEngine() throws -> (AVAudioEngine, SFSpeechAudioBufferRecognitionRequest) {
    // ... (setup audio engine and recognition request)
    return (audioEngine, request)
}
```

Sources: [Scrumdinger/Models/SpeechRecognizer.swift:117-137]()

### Recognition Handler

The `recognitionHandler(audioEngine:result:error:)` method is called by the `SFSpeechRecognitionTask` whenever a recognition result or error is received. It stops the audio engine and removes the input tap when a final result or an error is received.

```swift
nonisolated private func recognitionHandler(audioEngine: AVAudioEngine, result: SFSpeechRecognitionResult?, error: Error?) {
    let receivedFinalResult = result?.isFinal ?? false
    let receivedError = error != nil

    if receivedFinalResult || receivedError {
        audioEngine.stop()
        audioEngine.inputNode.removeTap(onBus: 0)
    }

    if let result {
        transcribe(result.bestTranscription.formattedString)
    }
}
```

Sources: [Scrumdinger/Models/SpeechRecognizer.swift:138-150]()

### Transcript Processing

The `transcribe(_:)` method is called with the transcribed text from the speech recognition result. It performs the following tasks:

1. Updates the `transcript` property with the new transcribed text.
2. Splits the transcript into individual words.
3. Counts the occurrences of profane words in the transcript.
4. Updates the user's profanity count and word frequency map in the Realm database.
5. Triggers device vibration when a profane word is detected.

```swift
nonisolated private func transcribe(_ message: String) {
    Task { @MainActor in
        transcript = String(message.lowercased().suffix(max(message.count - lastString.count, 0)))
        let components = transcript.components(separatedBy: " ")
        var map = [String : Int]()
        for word in components {
            if (await profanitySet.contains(word)) {
                if map.keys.contains(word) {
                    map[word]! += 1
                } else {
                    map[word] = 1
                }
                profanityCount = profanityCount + 1
                UIDevice.vibrate()
            }
        }
        if let user = users.first {
            try! realm.write {
                for key in map.keys {
                    user.freqMap[key] = (user.freqMap[key] ?? 0) + map[key]!
                }
                user.totalScore = 0
                for key in user.freqMap.keys {
                    user.totalScore = user.totalScore + (user.freqMap[key] ?? 0)
                }
            }
        }
        lastString = message.lowercased()
    }
}
```

Sources: [Scrumdinger/Models/SpeechRecognizer.swift:153-187]()

### Error Handling

The `transcribe(_:)` method is also used to handle errors that occur during the speech recognition process. It updates the `transcript` property with an error message.

```swift
nonisolated private func transcribe(_ error: Error) {
    var errorMessage = ""
    if let error = error as? RecognizerError {
        errorMessage += error.message
    } else {
        errorMessage += error.localizedDescription
    }
    Task { @MainActor [errorMessage] in
        transcript = "<< \(errorMessage) >>"
    }
}
```

Sources: [Scrumdinger/Models/SpeechRecognizer.swift:189-199]()

## Utility Methods

The `SpeechRecognizer` class provides several utility methods for controlling the speech recognition process and managing the profanity set.

### Starting and Stopping Transcription

The `startTranscribing()` and `stopTranscribing()` methods are used to start and stop the speech recognition process, respectively.

```swift
@MainActor func startTranscribing() {
    Task {
        await transcribe()
    }
}

@MainActor func stopTranscribing() {
    Task {
        await reset()
    }
}
```

Sources: [Scrumdinger/Models/SpeechRecognizer.swift:31-36]()

The `reset()` method cancels the current recognition task, stops the audio engine, and resets the associated properties.

```swift
private func reset() {
    task?.cancel()
    audioEngine?.stop()
    audioEngine = nil
    request = nil
    task = nil
}
```

Sources: [Scrumdinger/Models/SpeechRecognizer.swift:103-108]()

### Resetting Transcript and Profanity Set

The `resetTranscript()` method resets the transcript by calling the `reset()` method.

```swift
@MainActor func resetTranscript() {
    Task {
        await reset()
    }
}
```

Sources: [Scrumdinger/Models/SpeechRecognizer.swift:33-36]()

The `resetProfanity()` method retrieves the user's profanity set from the Realm database and updates the `profanitySet` property.

```swift
@MainActor func resetProfanity() {
    Task {
        await getProfanity()
    }
}

private func getProfanity() {
    Task {@MainActor in
        if let user = users.first {
            profanitySet = []
            for word in user.profanitySet {
                profanitySet.append(word)
            }
        }
    }
}
```

Sources: [Scrumdinger/Models/SpeechRecognizer.swift:25-28](), [Scrumdinger/Models/SpeechRecognizer.swift:71-79]()

## Conclusion

The "Speech Recognition Integration" feature in this project provides a comprehensive solution for real-time speech-to-text transcription and profanity detection. It leverages the `SFSpeechRecognizer` and `AVAudioEngine` classes to capture audio input, transcribe speech, and detect profane words. The `SpeechRecognizer` class manages the speech recognition process, updates the transcript, counts profanity occurrences, and interacts with the Realm database to store user information and word frequencies. This feature is designed to be used in SwiftUI views and ensures thread-safe access to its properties and methods through the use of actors.