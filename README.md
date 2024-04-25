
## Attempt 0
- Paste in markdown of endpoint page, ask to rewrite into scenario format, work conversationally 
- Input page: https://docs.aws.amazon.com/eks/latest/userguide/cluster-endpoint.html

## Attempt 1
- More formal prompt engineering approach
- Ask it to determine title and rewrite all in one go
- Input page: https://docs.aws.amazon.com/eks/latest/userguide/private-clusters.html

## Attempt 2
- Similar to attempt 1
- Added
    - "Rewrite the content following writing for the web best practices. Try to maintain the existing content, and make strategic changes to the content so that it's scenario-driven."
    - examples of good (in my opinion) titles

## Attempt 3
- decided to use chain prompting
- I asked for 3 titles
- I gave a different title after reviewing the generated one.
- I then asked for the rewrite.