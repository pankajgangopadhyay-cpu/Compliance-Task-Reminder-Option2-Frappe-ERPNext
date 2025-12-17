# Compliance & Task Reminder - Option 2A: Frappe/ERPNext

## Overview
Completely free, open-source, self-hosted Compliance & Task Reminder App using Frappe/ERPNext.
Full L1/L2/L3 escalation logic, automated reminders, professional dashboards, and complete audit trails.

## Key Features

✅ 100% Free & Open Source
✅ Completely Offline Capable
✅ Auto L1/L2/L3 Escalation Reminders
✅ Email + Push Notifications
✅ Professional Dashboards
✅ Audit Trail & Compliance Reports
✅ Role-Based Access Control
✅ Task Scheduling (One-time to Yearly)
✅ Mobile-Friendly Web App
✅ REST API Support

## Quick Start

### Windows (Easiest)

1. Download ERPNext Windows Installer: https://frappe.io/download
2. Run installer, accept defaults
3. Access at http://localhost:8000
4. Default: Admin / [password from installer]

### Linux (Ubuntu)

```bash
# Install Frappe Bench
sudo apt update
sudo apt install -y python3-dev python3-pip nodejs npm redis-server mariadb-server git

git clone https://github.com/frappe/bench.git
cd bench
sudo pip install -e .

# Initialize bench
bench init compliance-bench
cd compliance-bench

# Create site
bench new-site compliance.local

# Install ERPNext
bench get-app https://github.com/frappe/erpnext.git
bench --site compliance.local install-app erpnext

# Start server
bench start
```

### Docker

```bash
git clone https://github.com/frappe/frappe_docker.git
cd frappe_docker
docker-compose up -d
# Access at http://localhost
```

## Setup Compliance Module

### Step 1: Create Doctype - Compliance Task

1. Awesome Bar → Doctype (or customize form)
2. Create New Doctype: `Compliance Task`
3. Add Fields:
   - task_name (Text, Required)
   - description (Text Editor)
   - department (Link to Department)
   - owner_l1 (Link to User, Required)
   - manager_l2 (Link to User)
   - head_l3 (Link to User)
   - task_category (Select: Compliance, Audit, Maintenance, Security, Legal)
   - due_date (Date, Required)
   - frequency (Select: One-time, Daily, Weekly, Monthly, Quarterly, Yearly)
   - grace_period_days (Int)
   - status (Select: Open, In-Progress, Completed, Overdue, On Hold)
   - completion_pct (Percent)
   - completion_date (Date)
   - notes (Text Editor)
   - attachment (Attach)

4. Save & Create Form Layout

### Step 2: Create Escalation Matrix

1. Create Doctype: `Escalation Rule`
2. Fields:
   - task_category (Select)
   - l1_owner (Link to User)
   - l2_manager (Link to User)
   - l3_head (Link to User)
   - l1_reminder_days (Int)
   - l2_escalation_days (Int)
   - l3_escalation_days (Int)
   - l1_channel (Select: Email, Push, Both)
   - l2_channel (Select: Email, Push, Both)
   - l3_channel (Select: Email, Push, Both)

### Step 3: Create Reminder Log

1. Create Doctype: `Reminder Log`
2. Fields:
   - task (Link to Compliance Task)
   - reminder_level (Select: L1, L2, L3)
   - recipient (Link to User)
   - sent_date_time (DateTime)
   - channel (Select: Email, Push)
   - status (Select: Sent, Failed, Pending)
   - days_overdue (Int)

### Step 4: Setup Automated Workflows

1. Create Scheduled Job in Frappe
2. Go to: Awesome Bar → Automation → Scheduled Job Type
3. Create: "Check Compliance Reminders"
4. Frequency: Daily
5. Method: `compliance_module.tasks.check_all_reminders`

## Configuration Files

Create `compliance_module/config.py`:

```python
from frappe import _dict

config = _dict(
    app_name='Compliance & Task Reminder',
    remind_before_days={
        'L1': 1,
        'L2': 3,
        'L3': 5
    },
    email_config={
        'sender': 'compliance@yourcompany.com',
        'signature': 'Compliance Team'
    },
    roles={
        'Admin': 'Administrator',
        'Manager': 'Compliance Manager',
        'User': 'Employee'
    }
)
```

## Usage

### Create a Task

1. Awesome Bar → Compliance Task → New
2. Fill in:
   - Task Name
   - Department
   - Owner (L1)
   - Manager (L2)
   - Head (L3)
   - Due Date
   - Frequency
3. Save

### Track Completion

1. Owner updates Status
2. System auto-sends L2 reminder if overdue
3. System auto-escalates to L3 if critical
4. All sent in Reminder Log for audit

### View Reports

1. Awesome Bar → Report → Compliance Summary
2. Filter by Date, Department, Status
3. Export to PDF/Excel

## Troubleshooting

**Reminders not sending:**
- Check Scheduled Job: Awesome Bar → Scheduled Job
- Check Email Queue: Awesome Bar → Email Queue
- Verify SMTP settings: Setup → Email Account

**Can't access app:**
- Windows: Check http://localhost:8000
- Linux: Check if `bench start` is running
- Docker: Check `docker ps`

**Database issues:**
```bash
bench --site compliance.local migrate
bench --site compliance.local rebuild-search
```

## Support

- Frappe Docs: https://frappeframework.com
- ERPNext Docs: https://docs.erpnext.com
- Community: https://discuss.frappe.io

## License

MIT License - Free to use, modify, and distribute
