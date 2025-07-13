# AI-Powered Multi-Agent System for Industry level Stock Research
This system automates the process of generating professional-grade equity research reports for public companies. It replicates the work of human equity analysts by extracting, parsing, analyzing, and reporting on financial documents such as 10-K, 10-Q, earnings call summaries, valuation metrics, and financial news — all within minutes.
##  Overview  
###  Modular Architecture  
-  Scrapers: Fetch 10-K, 10-Q, earnings call transcripts, market data, and news  
-  Analytics Engine: Parse financials, detect risks, calculate key ratios  
-  LLM Agents: Multiple agents specialized in financial insight generation, sentiment evaluation, and valuation thesis building  
-  Benchmarking Module: Peer comparison and risk deltas across time  
-  Report Generator: Automated multi-page analyst brief in Markdown and PDF formats  
-  Streamlit UI: Interactive frontend for enterprise users, analysts, and clients

- ## 📂 File Structure  

```bash
equity-research-agent/
│
├── data/                  # Raw + processed financial data  
├── scrapers/              # EDGAR, Yahoo Finance, NewsAPI  
├── agents/                # LLM-driven analysis agents  
├── analytics/             # Parsing logic + ratio calculators  
├── report_gen/            # Report rendering (Markdown, PDF)  
├── streamlit_ui/          # Frontend dashboard  
├── utils/                 # Configs, logging, helpers  
├── tests/                 # Unit tests  
├── main.py                # Orchestration script  
├── README.md              # This file  
├── requirements.txt       # Python dependencies  
├── LICENSE                # MIT License

```

 ## Frontend Features
	-	Upload or enter stock ticker
	-	Choose timeframes or compare filings
	-	Toggle valuation models (P/E, EV/EBITDA, DCF)
	-	Download 5–8 page research briefs
	-	Risk radar dashboard and peer stack comparison

## Tech Stack
	-	Python 3.11
	-	OpenAI API 
	-	SEC EDGAR + Yahoo Finance + NewsAPI
	-	LangChain
  - Pandas, NumPy, Matplotlib
	-	Streamlit



### License

This project is licensed under the MIT License.


### Authors

Built and maintained by Krishna Gangwal. Inspired by real-world equity research automation needs.


### Contact

For enterprise deployment or collaboration:
krishnagangwal@icloud.com






