\=\# airbnb-clone-project

## Team Roles

- Backend Developer: Responsible for implementing API endpoints, database schemas, and business logic.
- Database Administrator: Manages database design, indexing, and optimizations.
- DevOps Engineer: Handles deployment, monitoring, and scaling of the backend services.
- QA Engineer: Ensures the backend functionalities are thoroughly tested and meet quality standards.

## Technology Stack

- Django: A high-level Python web framework used for building the RESTful API.
- Django REST Framework: Provides tools for creating and managing RESTful APIs.
- PostgreSQL: A powerful relational database used for data storage.
- GraphQL: Allows for flexible and efficient querying of data.
- Celery: For handling asynchronous tasks such as sending notifications or processing payments.
- Redis: Used for caching and session management.
- Docker: Containerization tool for consistent development and deployment environments.
- CI/CD Pipelines: Automated pipelines for testing and deploying code changes.

## Feature Breakdown

### 1. User Management
The user management system handles registration, authentication, and profile management for both guests and hosts. It provides secure login/logout functionality, profile customization, and role-based access control to distinguish between regular users and property hosts. This feature serves as the foundation for all personalized interactions within the platform.

### 2. Property Management
Property management enables hosts to create, edit, and manage their rental listings with comprehensive details including descriptions, pricing, amenities, and availability calendars. The system supports multiple property images, location mapping, and dynamic pricing strategies. This feature is crucial for hosts to showcase their properties effectively and attract potential guests.

### 3. Search and Discovery
The search and discovery system allows guests to find properties based on location, dates, price range, amenities, and other filters. It includes advanced filtering options, map-based search, and intelligent recommendations based on user preferences and booking history. This feature ensures guests can easily find properties that match their specific needs and preferences.

### 4. Booking System
The booking system manages the entire reservation process from availability checking to confirmation and cancellation handling. It prevents double bookings through real-time availability updates, calculates total costs including taxes and fees, and manages booking statuses throughout the guest's journey. This feature is the core transaction mechanism that connects guests with hosts.

### 5. Payment Processing
The payment processing system securely handles all financial transactions including booking payments, security deposits, and host payouts. It integrates with external payment providers, supports multiple payment methods and currencies, and manages refunds and dispute resolution. This feature ensures secure and reliable financial transactions for both guests and hosts.

### 6. Review and Rating System
The review and rating system allows guests to provide feedback on their stays and helps future guests make informed decisions. It includes detailed ratings for different aspects like cleanliness, communication, and location, along with written reviews and photo uploads. This feature builds trust and transparency within the platform community.

### 7. Communication System
The communication system facilitates secure messaging between guests and hosts before, during, and after bookings. It includes real-time notifications, message threading by property or booking, and automated system messages for booking updates. This feature ensures clear communication and helps resolve questions or issues promptly.

### 8. Notification System
The notification system keeps users informed about important events such as booking confirmations, payment receipts, check-in reminders, and new messages. It supports multiple notification channels including email, SMS, and in-app notifications with user-customizable preferences. This feature enhances user engagement and ensures critical information is communicated effectively.

## Database Design

### Key Entities and Relationships

#### 1. User
**Important Fields:**
- `id` (Primary Key): Unique identifier
- `email`: User's email address (unique)
- `password_hash`: Encrypted password
- `first_name`: User's first name
- `last_name`: User's last name
- `phone_number`: Contact number
- `profile_picture`: URL to profile image
- `is_host`: Boolean indicating if user can list properties
- `created_at`: Account creation timestamp
- `updated_at`: Last profile update timestamp

**Relationships:**
- One-to-many with Properties (as host)
- One-to-many with Bookings (as guest)
- One-to-many with Reviews (as reviewer)
- One-to-many with Messages

#### 2. Property
**Important Fields:**
- `id` (Primary Key): Unique identifier
- `host_id` (Foreign Key): References User who owns the property
- `title`: Property listing title
- `description`: Detailed property description
- `address`: Full address of the property
- `city`: City where property is located
- `country`: Country where property is located
- `price_per_night`: Nightly rate in currency
- `max_guests`: Maximum number of guests allowed
- `bedrooms`: Number of bedrooms
- `bathrooms`: Number of bathrooms
- `amenities`: JSON field storing available amenities
- `is_available`: Boolean indicating current availability
- `created_at`: Listing creation timestamp
- `updated_at`: Last update timestamp

**Relationships:**
- Many-to-one with User (host)
- One-to-many with Bookings
- One-to-many with Reviews
- One-to-many with PropertyImages
- One-to-many with Messages

#### 3. Booking
**Important Fields:**
- `id` (Primary Key): Unique identifier
- `property_id` (Foreign Key): References the booked Property
- `guest_id` (Foreign Key): References the User making the booking
- `check_in_date`: Start date of the stay
- `check_out_date`: End date of the stay
- `total_price`: Total cost of the booking
- `number_of_guests`: Number of guests for this booking
- `status`: Booking status (pending, confirmed, cancelled, completed)
- `special_requests`: Any special requests from guest
- `created_at`: Booking creation timestamp
- `updated_at`: Last status update timestamp

**Relationships:**
- Many-to-one with Property
- Many-to-one with User (guest)
- One-to-one with Payment
- One-to-many with Messages

#### 4. Review
**Important Fields:**
- `id` (Primary Key): Unique identifier
- `property_id` (Foreign Key): References the reviewed Property
- `reviewer_id` (Foreign Key): References the User writing the review
- `booking_id` (Foreign Key): References the associated Booking
- `rating`: Numeric rating (1-5 stars)
- `comment`: Written review text
- `cleanliness_rating`: Specific rating for cleanliness
- `communication_rating`: Rating for host communication
- `location_rating`: Rating for property location
- `created_at`: Review creation timestamp

**Relationships:**
- Many-to-one with Property
- Many-to-one with User (reviewer)
- One-to-one with Booking

#### 5. Payment
**Important Fields:**
- `id` (Primary Key): Unique identifier
- `booking_id` (Foreign Key): References the associated Booking
- `amount`: Payment amount
- `currency`: Currency code (USD, EUR, etc.)
- `payment_method`: Payment method used (card, paypal, etc.)
- `transaction_id`: External payment processor transaction ID
- `status`: Payment status (pending, completed, failed, refunded)
- `payment_date`: When payment was processed
- `created_at`: Payment record creation timestamp

**Relationships:**
- One-to-one with Booking

#### 6. Message
**Important Fields:**
- `id` (Primary Key): Unique identifier
- `sender_id` (Foreign Key): References User sending the message
- `recipient_id` (Foreign Key): References User receiving the message
- `property_id` (Foreign Key): References Property being discussed (optional)
- `booking_id` (Foreign Key): References Booking being discussed (optional)
- `content`: Message text content
- `is_read`: Boolean indicating if message has been read
- `created_at`: Message creation timestamp

**Relationships:**
- Many-to-one with User (sender)
- Many-to-one with User (recipient)
- Many-to-one with Property (optional)
- Many-to-one with Booking (optional)

#### 7. PropertyImage
**Important Fields:**
- `id` (Primary Key): Unique identifier
- `property_id` (Foreign Key): References the associated Property
- `image_url`: URL to the image file
- `alt_text`: Alternative text for accessibility
- `is_primary`: Boolean indicating if this is the main property image
- `display_order`: Order in which images should be displayed

**Relationships:**
- Many-to-one with Property

### Entity Relationship Summary

1. **User → Property**: One-to-many (A user can host multiple properties)
2. **User → Booking**: One-to-many (A user can make multiple bookings)
3. **User → Review**: One-to-many (A user can write multiple reviews)
4. **User → Message**: One-to-many (A user can send/receive multiple messages)
5. **Property → Booking**: One-to-many (A property can have multiple bookings)
6. **Property → Review**: One-to-many (A property can have multiple reviews)
7. **Property → PropertyImage**: One-to-many (A property can have multiple images)
8. **Booking → Payment**: One-to-one (Each booking has one payment)
9. **Booking → Review**: One-to-one (Each booking can have one review)
10. **Booking → Message**: One-to-many (A booking can generate multiple messages)

