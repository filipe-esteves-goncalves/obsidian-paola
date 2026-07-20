---
cssclasses:
  - wide-page
---

```swift
public protocol BetService {
    func loadBets() async throws -> [Bet]
    func saveBets(_ bets: [Bet]) async throws
}

// 1. Assume Bet is a struct for value semantics
public struct Bet {
    public var name: String
    public var sellIn: Int
    public var quality: Int
}

// 2. Define our Policy signature as a pure function
public typealias BetUpdatePolicy = (Bet) -> Bet

// 3. Encapsulate the strategies
public enum BetPolicies {
    
    public static func standard(bet: Bet) -> Bet {
        var updated = bet
        updated.sellIn -= 1
        
        let degradation = updated.sellIn < 0 ? 2 : 1
        updated.quality = max(0, updated.quality - degradation)
        
        return updated
    }
    
    public static func winningTeam(bet: Bet) -> Bet {
        // "Winning team" is legendary; it never ages or drops in quality.
        return bet
    }
    
    public static func playerPerformance(bet: Bet) -> Bet {
        var updated = bet
        updated.sellIn -= 1
        
        let improvement = updated.sellIn < 0 ? 2 : 1
        updated.quality = min(50, updated.quality + improvement)
        
        return updated
    }
    
    public static func totalScore(bet: Bet) -> Bet {
        var updated = bet
        updated.sellIn -= 1
        
        if updated.sellIn < 0 {
            updated.quality = 0
        } else if updated.sellIn < 5 {
            updated.quality += 3
        } else if updated.sellIn < 10 {
            updated.quality += 2
        } else {
            updated.quality += 1
        }
        
        updated.quality = min(50, updated.quality)
        return updated
    }
    
    // 4. The Strategy Factory
    public static func policy(for name: String) -> BetUpdatePolicy {
        switch name {
        case "Winning team":
            return winningTeam
        case "Player performance":
            return playerPerformance
        case "Total score":
            return totalScore
        default:
            return standard
        }
    }
}

// 5. The Refactored Repository
public class BetRepository {
    private let service: BetService

    public init(service: BetService) {
        self.service = service
    }

    public func updateOdds() async throws -> [Bet] {
        let bets = try await service.loadBets()
        
        // Functional approach: map over the array and apply the correct policy
        let updatedBets = bets.map { bet in
            let policy = BetPolicies.policy(for: bet.name)
            return policy(bet)
        }
        
        try await service.saveBets(updatedBets)
        return updatedBets
    }
}
```