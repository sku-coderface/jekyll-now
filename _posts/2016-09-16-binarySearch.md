---
layout: post
title: Binary Search Algorithm Writtn in Swift 3.0
author: miles3898
---

```swift
enum SearchError: Error{
    case NotSrotedError
    case CanNotFindSuchItemError
}

extension Array where Element: Comparable{
    func binarySearch(item: Element) throws ->Int{
        guard self == sorted(by: <) else{
            throw SearchError.NotSrotedError
        }

        var low = 0
        var high = self.count
        var mid = (low + high)/2

        while low <= high {
            switch self[mid]{
            case item:
                return mid
            case let x where x > item:
                high = mid - 1
            case let x where x < item:
                low = mid + 1
            default:
                break
            }

            mid = (low + high)/2
        }
        throw SearchError.CanNotFindSuchItemError
    }
}

let myList = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
let whatINeed = 7

do{
    let result = try myList.binarySearch(item: whatINeed)
    print("found \(whatINeed) at \(result)'th index!")
}catch let error{
    print(error)
}
```
