# 🧠 Local ELK Stack for Log Pattern Testing (Docker Compose Setup)

This repository sets up a local ELK Stack (Elasticsearch, Logstash, Kibana) using Docker Compose. It allows users to test and visualize custom hardcoded logs in Kibana through a predefined Logstash pipeline.

---

## 🛠 Stack Overview

The following services are included in the `docker-compose.yml`:

- **Elasticsearch** – Data storage and full-text search engine.  
- **Logstash** – Pipeline for log ingestion, parsing, and transformation.  
- **Kibana** – Data visualization and exploration frontend.  

---

## 📁 Project Structure

```
Logstash/
├── config/
│   └── logstash.yml
├── logs/
│   └── required_logs.log
└── pipeline/
    └── logstash.conf
```

---

### 📂 Folder Details

#### `config/`

Contains Logstash settings:

```yaml
# logstash.yml
http.host: "0.0.0.0"
xpack.monitoring.enabled: false
```

---

#### `logs/`

Add your hardcoded log file here.

Example:

```text
required_logs.log
```

---

#### `pipeline/`

Main pipeline configuration:

```conf
# logstash.conf
input {
  file {
    path => "/usr/share/logstash/logs/required_logs.log"
    start_position => "beginning"
    sincedb_path => "/dev/null"
  }
}

filter {
  # Customize based on log format
  grok {
    match => { "message" => "%{TIMESTAMP_ISO8601:timestamp} %{LOGLEVEL:level} %{GREEDYDATA:message}" }
  }
  date {
    match => ["timestamp", "ISO8601"]
  }
}

output {
  stdout { codec => rubydebug }
  elasticsearch {
    hosts => ["http://elasticsearch:9200"]
    index => "custom-logs"
  }
}
```

---

## 🚀 Getting Started

1. **Clone the Repository**

```bash
git clone https://github.com/your-username/your-repo.git
cd your-repo
```

2. **Run Docker Compose**

```bash
docker-compose up --build
```

This will bring up:

- Elasticsearch on `http://localhost:9200`  
- Kibana on `http://localhost:5601`  
- Logstash processing the logs

---

## 📊 Viewing Logs in Kibana

1. Open [http://localhost:5601](http://localhost:5601)  
2. Navigate to **Discover**  
3. Create a new index pattern: `custom-logs*`  
4. Start visualizing and exploring your logs!

---

## ✅ Notes

- Ensure the log format in `required_logs.log` matches the grok pattern in `logstash.conf`.
- You can extend or modify the filter section in the pipeline to match your custom log structure.
- To reload logs, restart Logstash or change the file to trigger fresh ingestion.
