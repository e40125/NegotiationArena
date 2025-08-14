# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

NegotiationArena is a Python research platform for evaluating LLM negotiation capabilities. The codebase prioritizes flexibility and ease of customization over strict OOP patterns - games are designed to be copied and modified rather than extended through inheritance.

## Commands

### Environment Setup
```bash
# Install dependencies
pip install -r requirements.txt

# Required .env file configuration:
OPENAI_API_KEY="your-key"
ANTHROPIC_API_KEY="your-key"
NEGOTIATION_LOG_FOLDER="/path/to/logs/"
ANY_SCALE="your-key"  # Optional for Llama
```

### Running Games
```bash
# Run specific game scenarios from project root
python runner/buysell_main.py
python runner/simple_game.py

# Launch web interface
cd webapp && streamlit run app.py
```

### Code Quality
```bash
# Format code (79 char line limit)
black . --line-length 79

# Run pre-commit hooks
pre-commit run --all-files
```

## Architecture

### Core Components (`negotiationarena/`)
- **Agent Classes**: Abstract `Agent` base class with implementations for ChatGPT (`ChatGPTAgent`), Claude (`ClaudeAgent`), and Llama (`Llama2Agent`)
- **Game Framework**: Abstract `Game` base class defining negotiation structure
- **Game Objects**: `Resources`, `Goals`, `Valuations`, `Trade` classes for game state
- **Utilities**: Custom JSON encoders, logging handlers, message parsing

### Game Implementations (`games/`)
- **buy_sell_game/**: Marketplace negotiation (complete)
- **simple_game/**: Basic resource exchange (complete)
- **trading_game/**: Complex trading (under refactoring)
- **ultimatum/**: Ultimatum game (under refactoring)

### Key Design Patterns

1. **Agent State Management**: Agents are stateless except for conversation history. Use `AGENT_ONE` ("Player RED") and `AGENT_TWO` ("Player BLUE") as standard identifiers.

2. **Game State Serialization**: All game states serialize to JSON for reproducibility. Use `negotiationarena.utils.SaveGame` and `LoadGame` for state management.

3. **Logging Architecture**: Dual logging system - structured JSON logs for analysis and human-readable logs for debugging. Always log to `NEGOTIATION_LOG_FOLDER`.

4. **LLM Integration**: Each agent type handles API calls differently:
   - ChatGPT: Uses `openai` library with retry logic
   - Claude: Uses `anthropic==0.5.0` with specific message formatting
   - Llama: Uses AnyScale API endpoint

## Development Guidelines

### Adding New Games
1. Copy an existing game directory (e.g., `simple_game/`) as template
2. Modify `game.py` to implement custom rules and objectives
3. Create corresponding runner script in `runner/`
4. Games should define `initial_state()`, `is_valid_action()`, `transition()`, and `compute_payoffs()`

### Extending Agent Capabilities
- Agents communicate via `generate_message()` and `receive_message()` methods
- Behavioral modifiers can be added through prompt engineering in agent initialization
- Keep agent logic separate from game logic for modularity

### Common Pitfalls
- Don't assume inheritance - games are designed to be copied and modified
- Always check environment variables before running agents
- Log files can grow large - implement rotation for long experiments
- API rate limits may affect multi-agent simulations

### Testing Considerations
No formal testing framework exists. When modifying:
- Test with example runners in `runner/` directory
- Verify JSON serialization/deserialization of game states
- Check conversation logs in `NEGOTIATION_LOG_FOLDER`
- Use webapp to visualize and validate results

## Current Limitations
- No automated testing framework
- Some games have hardcoded parameters that should be configurable
- Limited error recovery in runner scripts
- Web interface only supports visualization, not game execution

## Senior Dev Workflow - Priority Roadmap

### Week 1: Foundation (Make it Trustworthy)
```bash
# 1. Testing Framework
pytest tests/test_core_game.py  # Test game state transitions
pytest tests/test_agents.py     # Test agent message parsing
pytest tests/test_trades.py     # Test trade validation

# 2. Type Safety
mypy negotiationarena/ --strict  # Add type hints to core modules

# 3. Virtual Environment
python -m venv venv && source venv/bin/activate
pip install -r requirements.txt -r requirements-dev.txt
```

### Week 2: Operations (Make it Deployable)
```bash
# 1. Configuration Management
config/settings.yaml    # Extract all hardcoded values
config/prompts.yaml     # Centralize prompt templates

# 2. Error Handling
- Wrap all LLM calls with exponential backoff
- Add circuit breakers for API failures
- Implement fallback strategies

# 3. CI/CD Pipeline
.github/workflows/ci.yml  # pytest, black, mypy, coverage
.github/workflows/cd.yml  # Deploy to staging/prod
```

### Week 3: Scale (Make it Production-Ready)
```bash
# 1. Containerization
Dockerfile              # Multi-stage build
docker-compose.yml      # Local dev environment
k8s/                   # Kubernetes manifests

# 2. Observability
- Structured logging with correlation IDs
- Prometheus metrics for game outcomes
- Grafana dashboards for monitoring

# 3. API Layer
- FastAPI for RESTful game management
- WebSocket for real-time negotiation
- Rate limiting and authentication
```

### Critical Code Improvements

#### Immediate Fixes (Day 1)
```python
# negotiationarena/agent.py
class Agent(ABC):
    def __init__(self, ..., max_retries: int = 3, timeout: int = 30):
        # Add retry logic and timeouts
        
# games/game.py  
@dataclass
class GameConfig:
    """Extract all magic numbers"""
    max_turns: int
    initial_resources: Dict[str, int]
    
# Add .env validation
from pydantic import BaseSettings
class Settings(BaseSettings):
    openai_api_key: str
    anthropic_api_key: str
```

#### Testing Strategy
```python
# tests/test_game_logic.py
def test_trade_validation():
    """Can't trade resources you don't have"""
    
def test_game_termination():
    """Game ends when accept or max turns"""
    
def test_payoff_calculation():
    """Payoffs calculated correctly"""

# tests/test_integration.py
def test_gpt4_vs_claude():
    """Full game simulation with mocked APIs"""
```

#### Configuration Files
```yaml
# config/games/ultimatum.yaml
ultimatum:
  initial_amount: 100
  max_turns: 8
  rational_threshold: 1

# config/agents.yaml
agents:
  gpt4:
    model: "gpt-4-1106-preview"
    temperature: 0.7
    max_tokens: 400
    retry_attempts: 3
```

### Development Principles

1. **Test First** - No feature without tests
2. **Type Everything** - Catch errors at development time
3. **Configure, Don't Code** - All parameters in config files
4. **Fail Gracefully** - Every external call needs error handling
5. **Monitor Everything** - Can't improve what you don't measure

### Next PR Queue

1. **PR #1**: Core test suite (pytest + fixtures)
2. **PR #2**: Type hints for all public APIs
3. **PR #3**: Config management system
4. **PR #4**: Retry/error handling for LLM calls
5. **PR #5**: Docker + docker-compose setup

### Performance Optimizations

- Cache LLM responses for identical prompts
- Batch API calls where possible
- Use async for parallel game execution
- Implement connection pooling

### Security Considerations

- Never log API keys
- Sanitize all LLM outputs before parsing
- Rate limit by API key
- Implement request signing for webhooks