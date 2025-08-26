# Azure OpenAI PTU Estimator

🚀 **Enterprise-grade tool for optimizing Azure OpenAI costs through intelligent PTU sizing and burst pattern analysis**

Optimize your Azure OpenAI costs by analyzing real usage patterns and comparing PAYGO, PTU, and hybrid pricing models with comprehensive KQL integration and dynamic pricing from Azure APIs.

## 🎯 Key Features

### 📊 **Comprehensive Cost Analysis**
- **Real-time Pricing**: Dynamic pricing from Azure Retail Prices API with intelligent fallback
- **13 PTU Models**: Complete support for all PTU-enabled models including GPT-5 series
- **3 Deployment Types**: Global, Data Zone, and Regional deployment options
- **Hybrid Model Intelligence**: Smart base PTU sizing with spillover cost calculations

### 🔍 **Advanced KQL Integration**
- **Complete KQL Support**: All 7 KQL outputs (AvgTPM, P99TPM, MaxTPM, AvgPTU, P99PTU, MaxPTU, RecommendedPTU)
- **Burst Pattern Analysis**: Intelligent detection of usage patterns (Steady/Bursty/Spiky)
- **Smart Recommendations**: Data-driven PTU sizing based on actual Azure usage data
- **Cost Optimization**: Hybrid model recommendations with overflow calculations

### 🎨 **Professional User Experience**
- **Intuitive Interface**: Clean, enterprise-ready design with comprehensive explanations
- **Educational Content**: Built-in explanations for PTU concepts and hybrid models
- **Real-time Updates**: Live calculations as you input KQL results
- **Mobile Responsive**: Works perfectly on desktop and mobile devices

## 🚀 Quick Start

### Prerequisites
- Node.js 18+ and npm
- Modern web browser
- Azure subscription (for KQL queries)

### Local Development
```bash
# Clone the repository
git clone https://github.com/your-username/azure-openai-ptu-estimator.git
cd azure-openai-ptu-estimator

# Install dependencies
npm install

# Start development server
npm run dev

# Open http://localhost:5173 in your browser
```

### Production Build
```bash
# Build for production
npm run build

# Preview production build
npm run preview
```

## 📋 How to Use

### Step 1: Get Your Token Data
1. Navigate to your Azure Log Analytics workspace
2. Run the provided KQL query to analyze your usage patterns
3. Copy the results: `AvgTPM`, `P99TPM`, `MaxTPM`, `AvgPTU`, `P99PTU`, `MaxPTU`, `RecommendedPTU`

### Step 2: Input Parameters
1. **Select Region**: Choose your Azure region (shows PTU model count)
2. **Select Model**: Pick from 13 PTU-supported models (GPT-5, GPT-4o, etc.)
3. **Choose Deployment**: Global, Data Zone, or Regional deployment
4. **Enter KQL Results**: Input all 7 values from your KQL query
5. **Set Usage Parameters**: Monthly minutes and hybrid model base PTUs

### Step 3: Analyze Results
- **Cost Comparison**: PAYGO vs PTU vs Hybrid pricing
- **Burst Pattern Analysis**: Understanding your usage patterns
- **Smart Recommendations**: Data-driven PTU sizing advice
- **Savings Calculations**: 1-year and 3-year reservation benefits

## 🏗️ Architecture

### Frontend Stack
- **React 18**: Modern React with hooks and functional components
- **Vite**: Lightning-fast build tool and development server
- **Tailwind CSS**: Utility-first CSS framework for rapid styling
- **Shadcn/UI**: High-quality, accessible component library
- **Lucide Icons**: Beautiful, consistent icon set

### Pricing System
- **Dynamic API Integration**: Real-time pricing from Azure Retail Prices API
- **Intelligent Fallback**: Robust static pricing when API is unavailable
- **Smart Caching**: 3-hour cache with automatic refresh
- **Multi-Strategy Queries**: Multiple API query approaches for maximum coverage

### Data Processing
- **Burst Pattern Detection**: Advanced algorithms for usage pattern analysis
- **Cost Optimization Logic**: Multi-factor recommendation engine
- **Real-time Calculations**: Instant updates as parameters change
- **Validation Systems**: Input validation and error handling

## 🔧 Configuration

### Environment Variables
```bash
# Optional: Custom API endpoints
VITE_AZURE_PRICING_API=https://prices.azure.com/api/retail/prices
VITE_CACHE_DURATION=10800000  # 3 hours in milliseconds
```

### Pricing Configuration
The application includes comprehensive fallback pricing for all 13 PTU models:
- GPT-5 series (GPT-5, GPT-5 Mini, GPT-5 Nano, GPT-5 Chat)
- GPT-4 series (GPT-4o, GPT-4o Mini, GPT-4, GPT-4 Turbo)
- GPT-3.5 Turbo
- Embedding models (Ada 002, 3 Large, 3 Small)
- Whisper

## 📊 KQL Query

Use this query in Azure Log Analytics to get your usage data:

```kql
// Burst-Aware Azure OpenAI PTU Sizing Analysis
// Run this query in Azure Monitor Log Analytics for accurate capacity planning

let window = 1m;           // granularity for burst detection
let p = 0.99;             // percentile for burst sizing
AzureMetrics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where MetricName in ("ProcessedPromptTokens", "ProcessedCompletionTokens")
| where TimeGenerated >= ago(7d)
| summarize Tokens = sum(Total) by bin(TimeGenerated, window)
| summarize
    AvgTPM = avg(Tokens),
    P99TPM = percentile(Tokens, p),
    MaxTPM = max(Tokens)
| extend
    AvgPTU = ceiling(AvgTPM / 50000.0),
    P99PTU = ceiling(P99TPM / 50000.0),
    MaxPTU = ceiling(MaxTPM / 50000.0)
| extend RecommendedPTU = max_of(AvgPTU, P99PTU)  // higher value covers bursts
| project AvgTPM, P99TPM, MaxTPM, AvgPTU, P99PTU, MaxPTU, RecommendedPTU
```

## 🎯 Business Value

### Cost Optimization
- **Accurate Sizing**: Use real usage data instead of guesswork
- **Hybrid Intelligence**: Optimal base PTU + spillover calculations
- **Reservation Planning**: 1-year vs 3-year commitment analysis
- **Risk Reduction**: Avoid over-provisioning for bursty workloads

### Decision Support
- **Data-Driven**: Recommendations based on actual Azure usage patterns
- **Scenario Analysis**: Compare multiple pricing approaches
- **Burst Handling**: Understand and plan for traffic spikes
- **ROI Calculations**: Clear financial impact of different approaches

### Enterprise Ready
- **Professional Interface**: Clean, intuitive design for business users
- **Comprehensive Documentation**: Built-in explanations and guidance
- **Scalable Architecture**: Handles enterprise-scale usage analysis
- **Reliable Pricing**: Robust API integration with intelligent fallbacks

## 🚀 Deployment Options

### Option 1: Static Website Hosting
Deploy to any static hosting service (Netlify, Vercel, GitHub Pages):

```bash
# Build the application
npm run build

# Deploy the dist/ folder to your hosting service
```

### Option 2: Azure Static Web Apps
Perfect for Azure-native deployment:

```bash
# Install Azure CLI
az login

# Create Static Web App
az staticwebapp create \
  --name azure-openai-ptu-estimator \
  --resource-group your-resource-group \
  --source https://github.com/your-username/azure-openai-ptu-estimator \
  --location "East US 2" \
  --branch main \
  --app-location "/" \
  --output-location "dist"
```

### Option 3: Azure Container Apps
For containerized deployment:

```dockerfile
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

## 📁 Project Structure

```
azure-openai-ptu-estimator/
├── public/                 # Static assets
├── src/
│   ├── components/         # React components
│   │   └── ui/            # Shadcn/UI components
│   ├── enhanced_pricing_service.js  # Pricing API service
│   ├── ptu_supported_models.json   # PTU model definitions
│   ├── App.jsx            # Main application component
│   ├── App.css            # Application styles
│   └── main.jsx           # Application entry point
├── deployment/            # Deployment configurations
│   ├── azure-static-web-apps.yml
│   ├── dockerfile
│   └── bicep/            # Infrastructure as Code
├── docs/                 # Additional documentation
├── package.json          # Dependencies and scripts
├── vite.config.js        # Vite configuration
└── README.md            # This file
```

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guide](CONTRIBUTING.md) for details.

### Development Workflow
1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Make your changes and test thoroughly
4. Commit your changes: `git commit -m 'Add amazing feature'`
5. Push to the branch: `git push origin feature/amazing-feature`
6. Open a Pull Request

### Code Standards
- Use TypeScript for new features
- Follow React best practices
- Maintain test coverage above 80%
- Use conventional commit messages

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🆘 Support

### Documentation
- [Azure OpenAI Documentation](https://docs.microsoft.com/en-us/azure/cognitive-services/openai/)
- [PTU Pricing Guide](https://docs.microsoft.com/en-us/azure/cognitive-services/openai/concepts/provisioned-throughput)
- [KQL Reference](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/)

### Community
- [GitHub Issues](https://github.com/your-username/azure-openai-ptu-estimator/issues)
- [GitHub Discussions](https://github.com/your-username/azure-openai-ptu-estimator/discussions)

### Professional Support
For enterprise support and custom implementations, contact [your-email@domain.com](mailto:your-email@domain.com).

---

**Made with ❤️ for the Azure community**

*Optimize your Azure OpenAI costs with confidence using real data and intelligent analysis.*

