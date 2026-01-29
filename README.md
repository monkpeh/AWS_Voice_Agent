# Hotel Voice Agent

A voice-enabled hotel assistant built with AWS Bedrock Nova Sonic that provides real-time conversational AI capabilities for hotel guest services. The agent can verify guest identity, check reservations, view balances, and update reservation details through natural voice interactions.

## Features

- **Real-time Voice Interaction**: Bidirectional audio streaming with AWS Bedrock Nova Sonic model
- **Guest Identity Verification**: Secure verification using name and date of birth
- **Reservation Management**: Check upcoming reservations, view balances, and update room preferences
- **Tool Integration**: Async tool processing for database operations (DynamoDB)
- **Conversation Viewer**: Streamlit-based web UI to visualize conversation logs and token usage
- **Barge-in Support**: Interrupt handling for natural conversation flow

## Architecture

The project consists of three main components:

1. **`hotel_agent.py`**: Main voice agent application with bidirectional streaming
2. **`db_setup.py`**: Database setup script for DynamoDB tables and demo data
3. **`app.py`**: Streamlit web UI for viewing conversation logs

## Prerequisites

- Python 3.8 or higher
- AWS Account with Bedrock access (Nova Sonic model)
- AWS credentials with permissions for:
  - Bedrock Runtime
  - DynamoDB (create tables, read/write access)
- Microphone and speakers/headphones for audio I/O
- Windows/Linux/macOS with audio support

## Installation

1. **Clone the repository**:
   ```bash
   git clone <repository-url>
   cd mdl
   ```

2. **Create a virtual environment** (recommended):
   ```bash
   python -m venv .venv
   
   # On Windows
   .venv\Scripts\activate
   
   # On Linux/macOS
   source .venv/bin/activate
   ```

3. **Install dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

4. **Set up AWS credentials**:
   Create a `.env` file in the project root:
   ```env
   AWS_ACCESS_KEY_ID=your_access_key
   AWS_SECRET_ACCESS_KEY=your_secret_key
   AWS_DEFAULT_REGION=us-east-1
   ```

   **Note**: The `.env` file is already in `.gitignore` to keep credentials secure.

5. **Set up the database**:
   ```bash
   python db_setup.py
   ```
   
   This will:
   - Create DynamoDB tables (`Hotel_Guests` and `Hotel_Reservations`)
   - Seed demo data with sample guests and reservations

## Usage

### Running the Voice Agent

Start the voice agent:
```bash
python hotel_agent.py
```

For debug mode (verbose logging):
```bash
python hotel_agent.py --debug
```

**How to use**:
1. The application will initialize the Bedrock stream
2. Speak into your microphone when prompted
3. The agent will respond with audio output
4. Press Enter to stop streaming and exit

**Example interactions**:
- "Hi, I'm Anna Smith, date of birth June 5th, 1991. Can you check my reservation?"
- "What's my balance due?"
- "I'd like to change my room type to a suite"
- "Can you add feather-free pillows to my special requests?"

### Viewing Conversation Logs

Launch the Streamlit conversation viewer:
```bash
streamlit run app.py
```

The web UI will:
- Display conversation history from `assistant.log`
- Show token usage statistics
- Highlight tool calls and events
- Auto-refresh every 3 seconds

Access the UI at `http://localhost:8501` in your browser.

## Project Structure

```
.
├── hotel_agent.py      # Main voice agent application
├── db_setup.py         # Database setup and seeding script
├── app.py              # Streamlit conversation viewer
├── requirements.txt    # Python dependencies
├── .env               # AWS credentials (not in git)
├── assistant.log      # Conversation log file (generated at runtime)
└── README.md          # This file
```

## Key Components

### BedrockStreamManager
Manages bidirectional streaming with AWS Bedrock:
- Handles session initialization
- Processes audio input/output
- Manages tool calls and responses
- Supports barge-in detection

### ToolProcessor
Processes tool calls asynchronously:
- `checkGuestProfileTool`: Look up guest profiles
- `checkReservationStatusTool`: Check reservations and balances
- `updateReservationTool`: Update room types and special requests

### AudioStreamer
Handles audio I/O:
- Microphone input streaming
- Audio output playback
- Real-time audio processing

## Demo Data

The `db_setup.py` script creates two demo guests:

**Anna Smith** (Gold member, VIP)
- Upcoming reservation: RES-1001 (fully paid)
- Room: 1205, King Deluxe
- Special requests: High floor, Late check-out

**Mark Johnson** (Standard member)
- Upcoming reservation: RES-2001 (balance due: $240.50)
- Room: 0803, Queen Standard
- Special requests: Airport pickup, Feather-free pillows
- Past stay: RES-1999 (checked out)

## Configuration

### Audio Settings
Default audio configuration (in `hotel_agent.py`):
- Input sample rate: 16 kHz
- Output sample rate: 24 kHz
- Format: 16-bit PCM
- Channels: Mono (1)

### Model Configuration
- Model: `amazon.nova-sonic-v1:0`
- Region: `us-east-1` (configurable)
- Max tokens: 1024
- Temperature: 0.7
- Top-p: 0.9

## Troubleshooting

### Audio Issues
- **No audio input**: Check microphone permissions and device selection
- **No audio output**: Verify speakers/headphones are connected and working
- **Audio lag**: Reduce `CHUNK_SIZE` or check system resources

### AWS Issues
- **Authentication errors**: Verify `.env` file has correct credentials
- **Bedrock access denied**: Ensure your AWS account has Bedrock access and Nova Sonic model access
- **DynamoDB errors**: Check IAM permissions for DynamoDB operations

### Log File Issues
- **Conversation viewer shows no data**: Ensure `assistant.log` exists and contains UTF-16 encoded data
- **Parsing errors**: Check log file encoding (should be UTF-16)

## Security Notes

- Never commit `.env` file or AWS credentials
- The agent verifies guest identity before revealing sensitive information
- Tool calls are processed securely through AWS SDK
- Log files may contain sensitive conversation data

## Development

### Debug Mode
Enable debug mode for detailed logging:
```bash
python hotel_agent.py --debug
```

### Extending Functionality
To add new tools:
1. Define tool schema in `BedrockStreamManager.start_prompt()`
2. Add tool handler in `ToolProcessor._run_tool()`
3. Implement execution logic in `ToolProcessor._execute_*()`

## License

[Add your license information here]

## Contributing

[Add contribution guidelines here]

## Support

For issues or questions, please [create an issue](link-to-issues) or contact the maintainers.
