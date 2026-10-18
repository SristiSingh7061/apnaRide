# Bike Share Backend — Friend-Based Two-Wheeler Sharing Platform

A Node.js + Express + MongoDB backend for a peer-to-peer two-wheeler sharing app
where users can only book vehicles from their **accepted friends** — a trust-network
approach built for the Indian college/community context.

## Setup

1. Install dependencies:
   ```
   npm install
   ```
2. Copy `.env.example` to `.env` and fill in values:
   ```
   cp .env.example .env
   ```
3. Make sure MongoDB is running locally (or use MongoDB Atlas URI in `.env`).
4. Start the dev server:
   ```
   npm run dev
   ```
   Server runs on `http://localhost:5000` by default.

## Folder Structure

```
bike-share-backend/
├── config/db.js            # MongoDB connection
├── models/                 # Mongoose schemas
│   ├── User.js
│   ├── Vehicle.js
│   ├── Friendship.js
│   ├── Booking.js
│   └── Rating.js
├── controllers/             # Business logic
├── routes/                  # API route definitions
├── middleware/auth.js       # JWT auth guard
├── server.js                # App entry point
└── .env.example
```

## Core Flow

1. User signs up / logs in → gets JWT token
2. User sends friend request → recipient accepts/rejects
3. Only after friendship is `accepted` can bookings happen between them
4. Vehicle owner lists their bike/scooter
5. Friend browses available friend-network vehicles and requests a booking
6. Owner accepts/rejects → on accept, vehicle marked `booked`
7. After ride, either party marks booking `completed` → vehicle becomes `available` again
8. Both parties can rate each other → updates `trustScore` on User

## API Endpoints

### Auth
| Method | Route | Description |
|---|---|---|
| POST | /api/auth/signup | Register new user |
| POST | /api/auth/login | Login, returns JWT |

### Users (auth required)
| Method | Route | Description |
|---|---|---|
| GET | /api/users/me | Get my profile |
| PUT | /api/users/me | Update profile / location |
| GET | /api/users/:id | Get public profile of another user |

### Vehicles (auth required)
| Method | Route | Description |
|---|---|---|
| POST | /api/vehicles | List a new vehicle |
| GET | /api/vehicles/mine | My vehicles |
| GET | /api/vehicles/friends | Available vehicles owned by my friends |
| PUT | /api/vehicles/:id | Update vehicle (fuel, status, location) |
| DELETE | /api/vehicles/:id | Remove vehicle |

### Friends (auth required)
| Method | Route | Description |
|---|---|---|
| POST | /api/friends/request | Send friend request `{ recipientId }` |
| PUT | /api/friends/respond/:id | Accept/reject `{ action: 'accepted'|'rejected' }` |
| GET | /api/friends | List my accepted friends |
| GET | /api/friends/pending | Incoming pending requests |

### Bookings (auth required)
| Method | Route | Description |
|---|---|---|
| POST | /api/bookings | Request a booking (must be friends with owner) |
| PUT | /api/bookings/:id/respond | Owner accepts/rejects |
| PUT | /api/bookings/:id/complete | Mark ride completed |
| GET | /api/bookings/mine | My bookings (as owner or requester) |

### Ratings (auth required)
| Method | Route | Description |
|---|---|---|
| POST | /api/ratings | Rate other party after completed booking |
| GET | /api/ratings/user/:id | View a user's ratings |

## Auth Header Format

All protected routes require:
```
Authorization: Bearer <token>
```

## Next Steps (not yet built)
- Real-time chat between requester and owner (Socket.io)
- Push notifications (FCM) on booking status changes
- Google Maps integration for live location and distance filtering
- Image upload for vehicle photos and college ID verification
- Rate limiting / input validation hardening
