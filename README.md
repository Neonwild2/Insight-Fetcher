# Shopify Store Insights Fetcher

A comprehensive Python application that fetches insights from Shopify stores without using the official Shopify API. This application provides detailed analysis of e-commerce stores including product catalogs, policies, FAQs, social media handles, and more.

## Features

### Mandatory Requirements ✅
- **Product Catalog**: Complete list of products with details
- **Hero Products**: Products featured on the homepage
- **Privacy Policy**: Store privacy policy content
- **Return/Refund Policies**: Store return and refund policies
- **FAQs**: Frequently asked questions and answers
- **Social Media Handles**: Instagram, Facebook, Twitter, TikTok, etc.
- **Contact Information**: Email, phone, address, contact forms
- **Brand Context**: About, mission, vision, company information
- **Important Links**: Order tracking, contact, blog, etc.

### Bonus Features 🚀
- **Competitor Analysis**: Find and analyze competitor stores
- **Database Persistence**: Store all insights in MySQL database
- **LLM Processing**: Use OpenAI to enhance and structure data

## Technology Stack

- **Backend**: FastAPI (Python)
- **Database**: MySQL with SQLAlchemy ORM
- **Web Scraping**: Selenium + BeautifulSoup + aiohttp
- **AI/ML**: OpenAI GPT for data enhancement
- **Async Processing**: asyncio for concurrent operations
- **Testing**: pytest for unit testing

## Installation

### Prerequisites

- Python 3.8+
- MySQL 8.0+
- Chrome/Chromium browser (for Selenium)
- OpenAI API key (optional, for LLM features)

### Setup

1. **Clone the repository**
```bash
git clone <repository-url>
cd shopify-insights-fetcher
```

2. **Create virtual environment**
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. **Install dependencies**
```bash
pip install -r requirements.txt
```

4. **Environment Configuration**
Create a `.env` file in the root directory:
```env
# Database Configuration
DATABASE_URL=mysql://username:password@localhost/shopify_insights

# OpenAI Configuration (optional)
OPENAI_API_KEY=your_openai_api_key_here
OPENAI_MODEL=gpt-3.5-turbo

# Scraping Configuration
REQUEST_TIMEOUT=30
MAX_RETRIES=3
DELAY_BETWEEN_REQUESTS=1.0
HEADLESS_BROWSER=true
BROWSER_TIMEOUT=30
REQUESTS_PER_MINUTE=60
```

5. **Database Setup**
```bash
# Create MySQL database
mysql -u root -p
CREATE DATABASE shopify_insights;
CREATE USER 'shopify_user'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON shopify_insights.* TO 'shopify_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;

# Initialize database tables
python -c "from src.database.connection import db_manager; db_manager.create_tables()"
```

## Usage

### Running the Application

1. **Start the FastAPI server**
```bash
python main.py
```

2. **Access the API documentation**
- Swagger UI: http://localhost:8000/docs
- ReDoc: http://localhost:8000/redoc

### API Endpoints

#### 1. Fetch Store Insights
```http
POST /fetch-insights
Content-Type: application/json

{
  "website_url": "https://example-store.myshopify.com"
}
```

**Response Example:**
```json
{
  "store_url": "https://example-store.myshopify.com",
  "timestamp": "2024-01-15T10:30:00Z",
  "success": true,
  "product_catalog": [
    {
      "title": "Product Name",
      "description": "Product description",
      "price": 29.99,
      "currency": "USD",
      "images": ["https://example.com/image1.jpg"],
      "category": "clothing"
    }
  ],
  "hero_products": [...],
  "privacy_policy": {
    "title": "Privacy Policy",
    "content": "Policy content...",
    "url": "https://example.com/privacy"
  },
  "faqs": [
    {
      "question": "Do you offer COD?",
      "answer": "Yes, we do offer COD for orders above ₹500"
    }
  ],
  "social_handles": [
    {
      "platform": "instagram",
      "handle": "example_store",
      "url": "https://instagram.com/example_store"
    }
  ],
  "contact_info": {
    "email": "contact@example.com",
    "phone": "+1-555-123-4567"
  },
  "brand_context": {
    "name": "Example Store",
    "description": "Premium fashion retailer",
    "about": "We are a premium fashion retailer..."
  },
  "important_links": [
    {
      "title": "Track Order",
      "url": "https://example.com/track",
      "description": "Track your order status"
    }
  ],
  "total_products": 150,
  "total_faqs": 25,
  "processing_time": 12.5
}
```

#### 2. Health Check
```http
GET /health
```

#### 3. API Information
```http
GET /
```

### Error Handling

The API returns appropriate HTTP status codes:
- **200**: Success
- **401**: Website not found or inaccessible
- **500**: Internal server error

## Architecture

### Project Structure
```
shopify-insights-fetcher/
├── main.py                          # FastAPI application entry point
├── requirements.txt                 # Python dependencies
├── .env                            # Environment configuration
├── src/
│   ├── config/
│   │   └── settings.py             # Application configuration
│   ├── models/
│   │   └── shopify_insights.py     # Pydantic data models
│   ├── services/
│   │   ├── shopify_insights_service.py  # Main orchestration service
│   │   ├── llm_processor.py        # OpenAI integration
│   │   └── scrapers/               # Scraping services
│   │       ├── product_scraper.py
│   │       ├── policy_scraper.py
│   │       ├── faq_scraper.py
│   │       ├── social_scraper.py
│   │       ├── contact_scraper.py
│   │       ├── brand_scraper.py
│   │       └── link_scraper.py
│   └── database/
│       ├── models.py               # SQLAlchemy models
│       └── connection.py           # Database connection
├── tests/                          # Unit tests
└── README.md                       # This file
```

### Key Components

1. **ShopifyInsightsService**: Main service that orchestrates all scraping operations
2. **Scrapers**: Specialized services for different types of data extraction
3. **LLMProcessor**: OpenAI integration for data enhancement and structuring
4. **Database Layer**: MySQL persistence with SQLAlchemy ORM
5. **FastAPI**: Modern, fast web framework for building APIs

## Scraping Strategy

### Multi-Method Approach
1. **JSON API**: First try `/products.json` for efficient product extraction
2. **Page Scraping**: Fallback to HTML parsing for complex pages
3. **Selenium**: Handle JavaScript-heavy content
4. **Pattern Matching**: Use common Shopify URL patterns
5. **Fallback Methods**: Multiple strategies for each data type

### Rate Limiting
- Configurable delays between requests
- Respectful scraping practices
- Configurable request limits per minute

## Testing

### Running Tests
```bash
# Install test dependencies
pip install pytest httpx

# Run tests
pytest tests/

# Run with coverage
pytest --cov=src tests/
```

### Test Structure
- Unit tests for each scraper service
- Integration tests for the main service
- API endpoint testing
- Database operation testing

## Configuration

### Environment Variables
| Variable | Description | Default |
|----------|-------------|---------|
| `DATABASE_URL` | MySQL connection string | Required |
| `OPENAI_API_KEY` | OpenAI API key | Optional |
| `REQUEST_TIMEOUT` | HTTP request timeout | 30s |
| `MAX_RETRIES` | Maximum retry attempts | 3 |
| `HEADLESS_BROWSER` | Run browser in headless mode | true |
| `BROWSER_TIMEOUT` | Browser page load timeout | 30s |

### Performance Tuning
- Adjust `REQUESTS_PER_MINUTE` for rate limiting
- Configure `DELAY_BETWEEN_REQUESTS` for politeness
- Set appropriate timeouts for your network conditions

## Deployment

### Production Considerations
1. **Database**: Use production MySQL instance
2. **Chrome Driver**: Ensure Chrome/Chromium is available
3. **Environment**: Set production environment variables
4. **Monitoring**: Add logging and monitoring
5. **Scaling**: Consider containerization with Docker

### Docker Deployment
```dockerfile
FROM python:3.9-slim

# Install Chrome and dependencies
RUN apt-get update && apt-get install -y \
    wget \
    gnupg \
    unzip \
    && wget -q -O - https://dl.google.com/linux/linux_signing_key.pub | apt-key add - \
    && echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list \
    && apt-get update \
    && apt-get install -y google-chrome-stable

# Install Python dependencies
COPY requirements.txt .
RUN pip install -r requirements.txt

# Copy application code
COPY . .

# Expose port
EXPOSE 8000

# Run application
CMD ["python", "main.py"]
```

## Contributing

### Development Setup
1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests for new functionality
5. Ensure all tests pass
6. Submit a pull request

### Code Style
- Follow PEP 8 guidelines
- Use type hints
- Add docstrings for all functions
- Write comprehensive tests

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Support

For questions and support:
- Create an issue in the GitHub repository
- Check the API documentation at `/docs`
- Review the test examples for usage patterns

## Roadmap

### Future Enhancements
- [ ] Real-time monitoring dashboard
- [ ] Advanced competitor analysis
- [ ] Market trend analysis
- [ ] Automated reporting
- [ ] Multi-language support
- [ ] Advanced caching strategies
- [ ] Machine learning insights
- [ ] API rate limiting and authentication

---

**Note**: This application is designed for educational and research purposes. Please ensure compliance with website terms of service and respect robots.txt files when scraping websites.
