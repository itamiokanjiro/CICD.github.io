---
title: OSCP-Exam-Report
date: 2025-06-02 13:00:14
tags:
---

install

    apt install texlive-latex-recommended texlive-fonts-extra texlive-latex-extra pandoc p7zip-full



upgrade

    sudo apt update
    sudo apt install ruby ruby-dev build-essential
    gem install bundler
    undle install
    sudo apt install texlive-full

download
    
    git clone https://github.com/noraj/OSCP-Exam-Report-Template-Markdown.git



export PDF
    pandoc src/OSCP-exam-report-template_whoisflynn_v3.2.md \
    -o output/OSCP-OS-XXXXX-Exam-Report.pdf \
    --from markdown+yaml_metadata_block+raw_html \
    --template eisvogel \
    --table-of-contents \
    --toc-depth 6 \
    --number-sections \
    --top-level-division=chapter \
    --highlight-style breezedark \
    --resource-path=.:src


