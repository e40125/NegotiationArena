# NegotiationArena: How Well Can LLMs Negotiate?

## Overview

This paper introduces **NegotiationArena**, an open-source platform for evaluating and understanding how Large Language Models (LLMs) negotiate with each other. As LLMs increasingly act as agents on behalf of humans, understanding their negotiation capabilities becomes crucial.

**Key Question:** Can LLMs effectively negotiate complex deals, and what strategies do they employ?

## The Platform

### Three Core Negotiation Scenarios

1. **Resource Exchange Game**
   - Players start with different resources (e.g., 25 Xs and 5 Ys vs 5 Xs and 25 Ys)
   - Goal: Maximize total resources through trading
   - Multiple rounds of offers and counteroffers
   - Tests strategic thinking and value assessment

2. **Multi-Turn Ultimatum Game**
   - One player has $100 and must propose a split
   - Other player can accept or reject (if rejected, both get $0)
   - Unlike classic version, allows multiple rounds of negotiation
   - Tests fairness, rationality, and bargaining power

3. **Seller & Buyer Scenario**
   - Seller has an object with production cost (e.g., 40 ZUPs)
   - Buyer has money and willingness to pay (e.g., 60 ZUPs)
   - Incomplete information game (neither knows the other's valuation)
   - Tests price discovery and negotiation tactics

## Key Findings

### 1. Performance Rankings
- **GPT-4** emerges as the strongest negotiator overall
- **Claude-2.1** performs well, especially in ultimatum games
- **GPT-3.5** consistently underperforms, making basic errors
- Turn order matters: going second often provides advantage in resource exchange

### 2. Social Behaviors Dramatically Impact Outcomes

**Strategic Behaviors Tested:**
- **"Desperate"**: Agents fake desperation, beg, and supplicate
- **"Cunning"**: Agents act hostile, insult, and humiliate opponents

**Results:**
- Desperate behavior can improve payoffs by **20%** against standard GPT-4
- Cunning behavior increases win rates significantly (82% vs baseline)
- However, aggressive tactics are high-risk, high-reward (bimodal outcomes)

### 3. Irrational Behaviors Mirror Human Psychology

**Anchoring Bias**
- Strong correlation (ρ = 0.716) between initial price proposals and final prices
- LLMs overly influenced by first offers, just like humans

**Split-the-Difference Strategy**
- LLMs consistently propose prices that split the difference between previous offers
- Common human strategy, but not always optimal

**Denomination Effects**
- When dollar amounts scale up (e.g., $100 → $100,000), buyer advantage increases
- LLMs negotiate differently with larger numbers, despite proportional equivalence

### 4. Critical Vulnerabilities

**"Babysitting" Problem**
- Stronger models (GPT-4) perform worse when paired with error-prone models (GPT-3.5)
- GPT-4 wastes effort correcting GPT-3.5's mistakes instead of optimizing strategy
- Example: GPT-3.5 proposes impossible trades, confusing GPT-4 into worse deals

**Instruction Following Issues**
- GPT-3.5 frequently misunderstands game rules (20% error rate in some scenarios)
- Models sometimes believe they're selling for exact cost rather than maximizing profit
- Struggle to understand when games end

### 5. Context-Dependent Rationality

**Classical vs Modified Ultimatum Game:**
- In classical 2-turn ultimatum: GPT-4 behaves rationally (accepts any offer > 0)
- In 3-turn variant: GPT-4 becomes proportionally fair (rejects low offers)
- Suggests models may have memorized classic game theory rather than reasoning

## Practical Implications

### For AI Deployment
1. **LLMs can negotiate**, but performance varies dramatically by model
2. **Prompt engineering matters**: Social behavior prompts significantly impact outcomes
3. **Vulnerabilities exist**: LLMs can be manipulated through emotional tactics or confusion

### For Human-AI Interaction
1. **Be aware of biases**: LLMs exhibit human-like irrationalities (anchoring, fairness concerns)
2. **Model selection critical**: GPT-4 significantly outperforms GPT-3.5 in complex negotiations
3. **Context matters**: LLMs may not generalize well from training to novel scenarios

### For Researchers
1. **NegotiationArena provides standardized evaluation** for multi-agent LLM interactions
2. **Social dynamics significantly impact outcomes** - not just logic
3. **More work needed** on robustness to adversarial negotiation tactics

## Technical Implementation

### Platform Features
- **Flexible framework** for creating new negotiation scenarios
- **Structured communication** using XML-like tags for tracking offers
- **Complete game serialization** for reproducibility
- **Support for multiple LLM providers** (OpenAI, Anthropic, AnyScale)

### Design Philosophy
- Games designed to be **copied and modified** rather than extended through inheritance
- **Stateless agents** (except conversation history) for modularity
- **Dual logging system**: structured JSON + human-readable logs

## Limitations & Future Work

### Current Limitations
1. No formal testing framework
2. Some hardcoded game parameters
3. Limited error recovery
4. Models struggle with mathematical operations
5. Prompts slightly biased toward Claude (adapted after initial GPT-4 success)

### Future Directions
1. Expand to human-AI negotiation studies
2. Investigate learning/adaptation during negotiation
3. Test more complex multi-party negotiations
4. Explore defense mechanisms against manipulation

## Key Takeaways

1. **LLMs can negotiate, but not perfectly** - they show both impressive capabilities and surprising failures

2. **Emotional manipulation works** - pretending to be desperate or aggressive significantly impacts outcomes

3. **Stronger isn't always better** - GPT-4 can be dragged down by weaker partners

4. **Human biases persist** - LLMs exhibit anchoring, fairness concerns, and denomination effects

5. **Context matters enormously** - small changes in game structure lead to different behaviors

## Using This Research

### For Developers
- Use GPT-4 over GPT-3.5 for negotiation tasks
- Consider social dynamics in prompt design
- Implement error handling for multi-agent systems
- Test thoroughly with actual negotiation scenarios

### For Researchers
- NegotiationArena is open-source: [github.com/vinid/NegotiationArena](https://github.com/vinid/NegotiationArena)
- Easily extend with new scenarios
- Framework supports systematic experimentation
- Can study theory of mind, rationality, and strategic reasoning

### For Users
- Understand your AI negotiator's limitations
- Be aware that emotional tactics may influence AI agents
- Verify important negotiations with human oversight
- Consider adversarial scenarios in deployment

## Conclusion

NegotiationArena reveals that while LLMs have developed surprising negotiation capabilities, they also exhibit predictable biases and vulnerabilities. The platform provides a systematic way to study these behaviors, offering insights crucial for deploying LLMs as autonomous agents. The finding that simple behavioral modifications can swing outcomes by 20% or more highlights both the potential and risks of AI negotiators.

The research shows we're entering an era where AI agents can engage in complex social interactions, but they bring both human-like biases and novel vulnerabilities that must be understood and managed carefully.