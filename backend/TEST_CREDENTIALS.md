# DispatchGrid v2 — Test Credentials & Quick Start

## How to Run

```powershell
# 1. Navigate to backend
cd backend

# 2. Activate virtual environment
venv\Scripts\activate

# 3. Install dependencies (first time only)
pip install -r requirements.txt

# 4. Seed test data
python -m app.seed.v2_seed

# 5. Start the server
python -m uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

**Swagger UI:** http://localhost:8000/docs  
**ReDoc:** http://localhost:8000/redoc  
**Health check:** http://localhost:8000/api/health

---

## Test Credentials

### Admin
| Field    | Value                        |
|----------|------------------------------|
| Email    | `admin@dispatchgrid.dev`     |
| Password | `Admin@1234`                 |
| Role     | `admin`                      |

### Customers
| Name          | Email                  | Password        |
|---------------|------------------------|-----------------|
| Alice Sharma  | `alice@example.com`    | `Customer@1234` |
| Bob Patel     | `bob@example.com`      | `Customer@1234` |
| Carol Nair    | `carol@example.com`    | `Customer@1234` |

### Technicians (pre-approved, online)
| Name          | Email                        | Password    | Cert Level | Specialty        |
|---------------|------------------------------|-------------|------------|------------------|
| Raj Kumar     | `raj.tech@example.com`       | `Tech@1234` | 4          | AC Service       |
| Priya Singh   | `priya.tech@example.com`     | `Tech@1234` | 3          | Plumbing         |
| Amit Verma    | `amit.tech@example.com`      | `Tech@1234` | 5          | Electrical       |
| Sunita Devi   | `sunita.tech@example.com`    | `Tech@1234` | 2          | Washing Machine  |
| Deepak Rao    | `deepak.tech@example.com`    | `Tech@1234` | 3          | Multi-skill      |

---

## API Summary — All 60+ Endpoints

### Auth  (`/api/auth`)
| Method | Endpoint                       | Description                    |
|--------|--------------------------------|--------------------------------|
| POST   | `/register/customer`           | Register new customer          |
| POST   | `/register/technician`         | Register new technician        |
| POST   | `/login`                       | Login, get JWT tokens          |
| POST   | `/refresh`                     | Refresh access token           |
| GET    | `/me`                          | Current user info              |
| POST   | `/logout`                      | Logout (stateless)             |

### Customer  (`/api/customer`) — requires `role=customer`
| Method | Endpoint                                  | Description                             |
|--------|-------------------------------------------|-----------------------------------------|
| GET    | `/profile`                                | Get own profile                         |
| PATCH  | `/profile`                                | Update name / phone                     |
| GET    | `/addresses`                              | List saved addresses                    |
| POST   | `/addresses`                              | Add a saved address                     |
| DELETE | `/addresses/{index}`                      | Remove a saved address                  |
| GET    | `/categories`                             | Browse active service categories        |
| GET    | `/nearby-technicians`                     | Find nearby available techs (Uber-style)|
| POST   | `/bookings`                               | Create booking (triggers auto-dispatch) |
| GET    | `/bookings`                               | List own bookings (paginated)           |
| GET    | `/bookings/{id}`                          | Booking detail + assigned tech          |
| POST   | `/bookings/{id}/cancel`                   | Cancel a booking                        |
| POST   | `/bookings/{id}/rate`                     | Submit rating after completion          |
| GET    | `/bookings/{id}/technician-location`      | Live technician location + ETA          |
| GET    | `/notifications`                          | List notifications                      |
| PATCH  | `/notifications/{id}/read`                | Mark notification read                  |

### Technician  (`/api/technician`) — requires `role=technician`
| Method | Endpoint                       | Description                              |
|--------|--------------------------------|------------------------------------------|
| GET    | `/profile`                     | Own technician profile                   |
| PATCH  | `/profile`                     | Update profile                           |
| PATCH  | `/availability`                | Toggle online/offline                    |
| PATCH  | `/location`                    | Update live location (lat/lng)           |
| GET    | `/offers`                      | Pending non-expired offers               |
| POST   | `/offers/{id}/accept`          | Accept a job offer                       |
| POST   | `/offers/{id}/reject`          | Reject with reason (triggers re-dispatch)|
| GET    | `/jobs`                        | Own job list (paginated + filterable)    |
| GET    | `/jobs/{id}`                   | Job detail                               |
| PATCH  | `/jobs/{id}/status`            | Update: en_route → in_progress → completed |
| POST   | `/jobs/{id}/notes`             | Add completion note                      |
| GET    | `/earnings`                    | Total earnings + daily breakdown         |
| GET    | `/ratings`                     | Ratings received                         |
| GET    | `/notifications`               | Notifications                            |
| PATCH  | `/notifications/{id}/read`     | Mark read                                |

### Admin  (`/api/admin`) — requires `role=admin`
| Method | Endpoint                               | Description                              |
|--------|----------------------------------------|------------------------------------------|
| GET    | `/dashboard/overview`                  | KPIs: customers, techs, jobs by status   |
| GET    | `/dashboard/dispatch`                  | Dispatch metrics, acceptance/rejection   |
| GET    | `/dashboard/workload`                  | Per-tech workload and utilisation        |
| GET    | `/dashboard/sla`                       | SLA compliance stats                     |
| GET    | `/dashboard/feedback`                  | Ratings, distribution, low-rating alerts |
| GET    | `/dashboard/revenue`                   | Revenue summary, top earners             |
| GET    | `/dashboard/demand`                    | Demand by hour, category, urgency        |
| GET    | `/customers`                           | List customers (search + filter)         |
| GET    | `/customers/{id}`                      | Customer detail + booking history        |
| PATCH  | `/customers/{id}`                      | Update customer                          |
| GET    | `/technicians`                         | List technicians (filters)               |
| GET    | `/technicians/{id}`                    | Technician detail                        |
| PATCH  | `/technicians/{id}`                    | Edit technician                          |
| PATCH  | `/technicians/{id}/approve`            | Approve technician account               |
| PATCH  | `/technicians/{id}/suspend`            | Suspend technician account               |
| GET    | `/jobs`                                | List all jobs (filters, pagination)      |
| GET    | `/jobs/{id}`                           | Job detail + offer history               |
| PATCH  | `/jobs/{id}/assign`                    | Manual assign to specific technician     |
| PATCH  | `/jobs/{id}/reassign`                  | Re-trigger auto-dispatch                 |
| PATCH  | `/jobs/{id}/cancel`                    | Admin cancel job                         |
| GET    | `/config/sla-tiers`                    | List SLA tiers                           |
| POST   | `/config/sla-tiers`                    | Create SLA tier                          |
| PATCH  | `/config/sla-tiers/{id}`               | Update SLA tier                          |
| DELETE | `/config/sla-tiers/{id}`               | Delete SLA tier                          |
| GET    | `/config/matching`                     | Matching engine config (weights etc.)    |
| PATCH  | `/config/matching`                     | Update matching weights/timeouts         |
| GET    | `/config/categories`                   | List service categories                  |
| POST   | `/config/categories`                   | Create category                          |
| PATCH  | `/config/categories/{id}`              | Update category                          |
| DELETE | `/config/categories/{id}`              | Soft-delete category                     |
| GET    | `/audit-log`                           | Immutable admin audit trail              |
| GET    | `/export/jobs`                         | CSV export of all jobs                   |
| GET    | `/export/technicians`                  | CSV export of all technicians            |

### Real-time  (WebSocket / SSE)
| Protocol | Endpoint                          | Description                        |
|----------|-----------------------------------|------------------------------------|
| WS       | `/ws/bookings/{id}`               | Live booking status updates        |
| WS       | `/ws/technician/offers`           | Real-time offer push to technician |
| SSE      | `/api/stream/booking/{id}/status` | Polling-friendly SSE fallback      |

---

## Running Tests

```powershell
cd backend
venv\Scripts\activate
python -m pytest tests/test_matching.py tests/test_sla.py tests/test_booking_flow.py -v
```

---

## Architecture Notes

| Component          | Detail                                                       |
|--------------------|--------------------------------------------------------------|
| Framework          | FastAPI (Python)                                             |
| Database           | SQLite via SQLAlchemy ORM                                    |
| Auth               | JWT HS256 — access token (30min) + refresh token (7 days)   |
| Matching engine    | Weighted score: distance(35%) rating(25%) workload(20%) skill(15%) acceptance(5%) |
| SLA monitoring     | APScheduler runs every 2 min; at-risk threshold = 80%        |
| Offer timeout      | Normal=120s, High-risk=60s; max 5 attempts then escalated    |
| Notifications      | In-app DB records (push/SMS adapter-ready)                   |
| Real-time          | WebSocket + SSE (polling fallback)                           |
| Location           | Haversine distance + 30 km/h ETA estimate                   |

