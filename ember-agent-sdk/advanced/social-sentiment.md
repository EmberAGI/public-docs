# Social Sentiment Analysis

Learn how to access and analyze social sentiment data using the Ember SDK.

## Getting Token Sentiment

Retrieve social sentiment data for a specific token:

```rust
use ember_sdk::{GetSocialSentimentParams, SentimentType, Timeframe};

async fn get_token_sentiment(client: &EmberClient) -> Result<(), EmberError> {
    let params = GetSocialSentimentParams {
        identifier: "0xTokenAddress...".to_string(),
        sentiment_type: SentimentType::Token,
        timeframe: Timeframe::Day,
    };

    let sentiment = client.get_social_sentiment(params).await?;
    
    println!("Sentiment score: {}", sentiment.score);
    println!("Mention volume: {}", sentiment.volume);
    println!("Trending: {}", sentiment.trending);
    
    // Print source-specific metrics
    println!("Twitter sentiment: {}", sentiment.sources.twitter.sentiment);
    println!("Reddit sentiment: {}", sentiment.sources.reddit.sentiment);
    println!("Discord sentiment: {}", sentiment.sources.discord.sentiment);
    
    Ok(())
}
```

## Protocol Sentiment Analysis

Analyze sentiment for entire protocols:

```rust
async fn get_protocol_sentiment(client: &EmberClient) -> Result<(), EmberError> {
    let params = GetSocialSentimentParams {
        identifier: "AAVE".to_string(),
        sentiment_type: SentimentType::Protocol,
        timeframe: Timeframe::Week,
    };

    let sentiment = client.get_social_sentiment(params).await?;
    
    // Analyze sentiment across different timeframes
    for timeframe in [Timeframe::Day, Timeframe::Week, Timeframe::Month] {
        let params = GetSocialSentimentParams {
            identifier: "AAVE".to_string(),
            sentiment_type: SentimentType::Protocol,
            timeframe,
        };
        
        let sentiment = client.get_social_sentiment(params).await?;
        println!("{:?} sentiment: {}", timeframe, sentiment.score);
    }
    
    Ok(())
}
```

## Source Analysis

Analyze sentiment from specific sources:

```rust
async fn analyze_sources(sentiment: &SocialSentiment) {
    // Twitter analysis
    let twitter = &sentiment.sources.twitter;
    println!("Twitter metrics:");
    println!("- Mentions: {}", twitter.mentions);
    println!("- Sentiment: {}", twitter.sentiment);
    
    // Reddit analysis
    let reddit = &sentiment.sources.reddit;
    println!("Reddit metrics:");
    println!("- Mentions: {}", reddit.mentions);
    println!("- Sentiment: {}", reddit.sentiment);
    
    // Discord analysis
    let discord = &sentiment.sources.discord;
    println!("Discord metrics:");
    println!("- Mentions: {}", discord.mentions);
    println!("- Sentiment: {}", discord.sentiment);
}
```

## Trend Detection

Monitor for trending tokens or protocols:

```rust
async fn monitor_trends(client: &EmberClient) -> Result<(), EmberError> {
    let tokens = vec![
        "0xToken1...".to_string(),
        "0xToken2...".to_string(),
        "0xToken3...".to_string(),
    ];
    
    for token in tokens {
        let params = GetSocialSentimentParams {
            identifier: token.clone(),
            sentiment_type: SentimentType::Token,
            timeframe: Timeframe::Day,
        };
        
        let sentiment = client.get_social_sentiment(params).await?;
        
        if sentiment.trending {
            println!("Token {} is trending!", token);
            println!("Sentiment score: {}", sentiment.score);
            println!("Volume: {}", sentiment.volume);
        }
    }
    
    Ok(())
}
```

## Real-time Monitoring

Set up real-time sentiment monitoring using WebSockets:

```rust
use ember_sdk::{EmberWebSocket, StreamSentimentParams};

async fn monitor_sentiment(ws: &EmberWebSocket) -> Result<(), EmberError> {
    let params = StreamSentimentParams {
        identifiers: vec![
            "0xToken1...".to_string(),
            "AAVE".to_string(),
        ],
        sentiment_types: vec![
            SentimentType::Token,
            SentimentType::Protocol,
        ],
    };

    ws.on_sentiment_update(|update| {
        println!("Sentiment update for {}", update.identifier);
        println!("New score: {}", update.score);
        println!("Change: {}", update.score_change);
        
        if update.score_change.abs() > 0.1 {
            println!("Significant sentiment change detected!");
        }
    }).await;
    
    Ok(())
}
```

## Best Practices

1. Consider multiple timeframes for trend analysis
2. Compare sentiment across different sources
3. Monitor for sudden sentiment changes
4. Use sentiment data alongside other metrics
5. Consider source reliability and weighting
6. Implement rate limiting for API calls
7. Set up alerts for significant sentiment changes 