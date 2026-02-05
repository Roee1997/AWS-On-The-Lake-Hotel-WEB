# On The Lake - AWS Serverless Hotel Booking Platform

A modern, full-stack hotel booking web application built entirely on AWS cloud infrastructure, featuring real-time room management, secure user authentication, comprehensive admin dashboard, and seamless booking experience.

## Screenshots

### Home Page
*Main landing page featuring hero carousel, room highlights, and hotel overview*

![Home Page](screenshots/1-home-page.jpeg)

### Rooms Overview
*Browse available rooms with advanced filtering by dates and guest count*

![Rooms Overview](screenshots/2-rooms-overview.jpeg)

### Room Details & Booking
*Room details, pricing, and seamless booking*

![Room Details](screenshots/3-room-details.jpeg)

### Admin Dashboard - Manage Rooms
*CRUD operations for managing all hotel rooms*

![Admin Manage Rooms](screenshots/4.admin%20crud%20manage%20room%20table.jpeg)

### Admin Dashboard - Add New Room
*Add new rooms with image upload and validation*

![Admin Add Room](screenshots/5-admin-add-room.jpeg)

### Admin Dashboard - Manage Bookings
*Admin interface for managing all bookings with approve/deny functionality*

![Admin Manage Bookings](screenshots/6-admin-approve-deny-manage-bookings.jpeg)

## Table of Contents
- [Key Features](#key-features)
- [Technology Stack](#technology-stack)
- [AWS Architecture](#aws-architecture)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [User Features](#user-features)
- [Admin Dashboard](#admin-dashboard)
- [AWS Services Integration](#aws-services-integration)
- [API Documentation](#api-documentation)
- [File Descriptions](#file-descriptions)
- [Development](#development)
- [Security](#security)
- [Future Enhancements](#future-enhancements)
- [Contributing](#contributing)
- [License](#license)

## Key Features

### **Core Functionality**
- **Serverless Architecture**: Built entirely on AWS managed services with zero server maintenance
- **Real-time Availability**: Instant room availability updates across all users
- **Secure Authentication**: AWS Cognito integration with OAuth 2.0 and JWT tokens
- **Role-Based Access Control**: Separate user and admin experiences with group-based permissions
- **Responsive Design**: Optimized for desktop, tablet, and mobile devices
- **Hebrew & English Support**: Bilingual content with cultural adaptation

### **Guest Experience**
- **Smart Room Search**: Filter by check-in/check-out dates and guest count (1-25 guests)
- **Interactive Room Catalog**: Beautiful room cards with images, pricing, and amenities
- **Seamless Booking Flow**: Pre-filled forms with user data and date validation
- **Booking Management**: Track all personal bookings with detailed status information
- **Custom Alerts**: Professional notification system replacing browser alerts
- **Local Attractions**: Curated list of nearby activities and points of interest

### **Admin Capabilities**
- **Comprehensive Dashboard**: Manage all bookings and rooms from a single interface
- **Booking Management**: Approve/deny reservations with automatic room availability updates
- **Room CRUD Operations**: Add, edit, delete rooms with full validation
- **Image Management**: Upload room images directly to S3 with preview
- **Real-time Updates**: Instant table refreshes without page reloads
- **Access Control**: Protected admin routes with authentication verification

### **Technical Excellence**
- **AWS Lambda Backend**: Serverless functions for all business logic
- **DynamoDB Database**: NoSQL storage with instant scalability
- **S3 Static Hosting**: Fast, reliable website delivery with 99.99% uptime
- **API Gateway**: RESTful API with automatic scaling and monitoring
- **CloudWatch Monitoring**: Hourly availability tracking with custom metrics dashboard
- **EventBridge Scheduling**: Automated room availability monitoring

## Technology Stack

### **Frontend Technologies**
| Technology | Version | Purpose |
|------------|---------|---------|
| **HTML5** | Latest | Semantic markup and structure |
| **CSS3** | Latest | Modern styling with animations |
| **JavaScript** | ES6+ | Client-side logic and interactions |
| **Bootstrap** | 5.0.0 | Responsive UI framework |
| **jQuery** | 3.4.1 | DOM manipulation and animations |
| **Flatpickr** | Latest | Modern date picker component |
| **Owl Carousel** | 2.3.4 | Image carousels and sliders |
| **WOW.js** | Latest | Scroll-triggered animations |
| **Animate.css** | Latest | CSS animation library |
| **Font Awesome** | 5.10.0 | Icon library |

### **AWS Cloud Services**
| Service | Purpose |
|---------|---------|
| **Amazon S3** | Static website hosting and room image storage |
| **Amazon DynamoDB** | NoSQL database for rooms, bookings, and images |
| **AWS Lambda** | Serverless backend functions (12+ functions) |
| **Amazon API Gateway** | RESTful API management and routing |
| **Amazon Cognito** | User authentication and authorization |
| **Amazon CloudWatch** | Monitoring, metrics, and logging |
| **Amazon EventBridge** | Scheduled task automation (hourly checks) |
| **Amazon CloudFront** | (Planned) CDN for global content delivery |

### **External Libraries**
- **Moment.js & Tempusdominus** - Date/time handling and UI
- **Waypoints** - Scroll event handling
- **CounterUp** - Animated statistics counters
- **Easing** - Animation timing functions

## AWS Architecture

### **System Architecture Overview**
The application implements a **serverless architecture** with complete separation of concerns:

```
User Browser → S3 Static Website (HTTPS)
            → Cognito (OAuth Login)
            → API Gateway (REST API)
            → Lambda Functions (Business Logic)
            → DynamoDB (Data Storage)
            → S3 (Image Storage)

EventBridge Scheduler → Monitoring Lambda → CloudWatch (Metrics Dashboard)
```

### **Data Flow Pattern**
```
User Action → JavaScript → API Gateway → Lambda Function → DynamoDB → Response → UI Update
```

### **AWS Services Architecture**

#### **Amazon DynamoDB Tables**

**1. rooms Table**
- **Primary Key**: room_id (String)
- **Attributes**:
  - RoomType (String) - "Family Room", "Deluxe Room", "Basic Room"
  - Description (String) - Detailed room information
  - PricePerNight (Number) - Room rate in USD
  - MaxGuests (Number) - Maximum occupancy (2-25 guests)
  - Available (Boolean) - Real-time availability status
  - Amenities (List) - ["Wifi", "TV", "Air Conditioning", etc.]
  - ImageURL (String) - S3 URL for room image

**2. bookRoom Table**
- **Primary Key**: booking_id (String, UUID)
- **Attributes**:
  - user_email (String) - Guest email from Cognito
  - user_name (String) - Guest name
  - room_id (String) - Reference to rooms table
  - room_type (String) - Denormalized for quick access
  - check_in (String, ISO Date) - Check-in date
  - check_out (String, ISO Date) - Check-out date
  - guest_count (Number) - Number of guests
  - additional_requests (String) - Special requests/notes
  - status (String) - "Pending", "Approved", "Denied"
  - time (String, ISO Timestamp) - Booking creation time

**3. room_images Table**
- **Primary Key**: image_id (String)
- **Attributes**:
  - room_id (String) - Associated room
  - image_url (String) - S3 object URL
  - upload_date (String, ISO Timestamp)
  - uploaded_by (String) - Admin email

#### **Amazon Cognito - User Pool**

**User Pool Name**: `MyCognitoUsersPool`
- **Region**: us-east-1
- **Authentication Flow**: OAuth 2.0 Implicit Grant
- **Token Type**: JWT (JSON Web Tokens)

**User Groups**:
1. **Users** (Regular Guests)
   - Browse available rooms
   - Create bookings
   - View personal booking history
   - View nearby attractions
   - Navigate to hotel via Waze integration

2. **Admin** (Hotel Managers)
   - All user capabilities
   - Access admin dashboard
   - Manage all bookings (approve/deny)
   - Add/edit/delete rooms
   - Upload room images
   - Monitor room availability

#### **AWS Lambda Functions**

**Public Functions** (No authentication required):
1. **getRoomsFunction** - Scans rooms table for Available=true, joins with room_images table, returns filtered room list

**User Functions** (Requires authentication):
2. **SaveBookingToDynamoDB** - Creates booking with unique ID, updates room availability
3. **showBookingsToClient** - Returns user bookings by email with room details
4. **getBookingsFunction** - Retrieves all bookings sorted by timestamp

**Admin Functions** (Requires admin group membership):
5. **getRoomsForAdmin** - Retrieves ALL rooms including unavailable
6. **postAdminAddRoom** - Adds new room with validation
7. **putAdminUpdateRoom** - Updates room details
8. **deleteAdminRoom** - Deletes room and associated images
9. **postAdminUploadRoomImage** - Uploads image to S3 and creates record
10. **updateBookingStatusFunction** - Updates booking status
11. **adminUpdateRoomAvailability** - Toggles room availability

**Monitoring Function**:
12. **cloudwatchRoomAvailabilityNumberAlert** - Counts available rooms hourly and publishes metric to CloudWatch

#### **Amazon API Gateway**

**API Endpoints**:

| Method | Path | Lambda Function | Auth | Description |
|--------|------|-----------------|------|-------------|
| GET | / | getRoomsFunction | No | Get available rooms |
| GET | /bookings | getBookingsFunction | Admin | Get all bookings |
| POST | /bookings | SaveBookingToDynamoDB | User | Create booking |
| GET | /myBookings | showBookingsToClient | User | Get user bookings |
| GET | /Admin/Rooms-Table | getRoomsForAdmin | Admin | Get all rooms |
| POST | /Admin/Rooms-Table | postAdminAddRoom | Admin | Add room |
| PUT | /Admin/Rooms-Table | putAdminUpdateRoom | Admin | Update room |
| DELETE | /Admin/Rooms-Table | deleteAdminRoom | Admin | Delete room |
| POST | /Admin/postAdminUploadRoomImage | postAdminUploadRoomImage | Admin | Upload image |
| POST | /Admin/update-room-availability | adminUpdateRoomAvailability | Admin | Toggle availability |
| POST | /Admin/updating-status | updateBookingStatusFunction | Admin | Approve/deny booking |

## Project Structure

```
AWS-Hotel-Web/
│
├── index.html                         # Homepage with hero carousel and hotel overview
├── room.html                          # Room catalog with search functionality
├── booking.html                       # Booking form with user details
├── myBookings.html                    # User's booking history
├── admin.html                         # Admin dashboard (protected)
├── footer.html                        # Reusable footer component
├── 404.html                           # Custom error page
│
├── css/                               # Stylesheets
│   ├── style.css                      # Main styles (primary: #FEA116)
│   ├── admin.css                      # Admin dashboard styles
│   ├── room.css                       # Room catalog styles
│   └── bootstrap.min.css              # Bootstrap framework
│
├── js/                                # JavaScript Application Logic
│   ├── login.js                       # Cognito OAuth integration (158 lines)
│   ├── customAlert.js                 # Custom notifications (115 lines)
│   ├── common.js                      # Footer loader (16 lines)
│   ├── main.js                        # UI animations (107 lines)
│   ├── fetchRooms.js                  # Room display (80 lines)
│   ├── roomSearchBar.js               # Date picker and filters (67 lines)
│   ├── roomToBooking.js               # Room selection handler (42 lines)
│   ├── bookingInfoFromRoom.js         # Form population (64 lines)
│   ├── submitBooking.js               # Booking submission (52 lines)
│   ├── myBookings.js                  # Bookings display (98 lines)
│   ├── adminAuthenticator.js          # Admin verification (37 lines)
│   ├── adminManageBookings.js         # Booking management (225 lines)
│   └── adminManageRooms.js            # Room CRUD operations (392 lines)
│
├── img/                               # Images and assets
├── lib/                               # External JavaScript libraries
└── scss/                              # SCSS source files
```

**Total Project Statistics**: 7 HTML pages | 13 JS files (~1,270 lines) | 4 CSS files | 12 Lambda functions | 11 API endpoints

## Getting Started

### **Prerequisites**
- AWS Account with appropriate IAM permissions
- AWS CLI installed and configured
- Node.js 14+ (for local development)
- Git for version control

### **AWS Services Setup**

#### **1. Create DynamoDB Tables**
```bash
aws dynamodb create-table --table-name rooms \
    --attribute-definitions AttributeName=room_id,AttributeType=S \
    --key-schema AttributeName=room_id,KeyType=HASH \
    --billing-mode PAY_PER_REQUEST

aws dynamodb create-table --table-name bookRoom \
    --attribute-definitions AttributeName=booking_id,AttributeType=S \
    --key-schema AttributeName=booking_id,KeyType=HASH \
    --billing-mode PAY_PER_REQUEST

aws dynamodb create-table --table-name room_images \
    --attribute-definitions AttributeName=image_id,AttributeType=S \
    --key-schema AttributeName=image_id,KeyType=HASH \
    --billing-mode PAY_PER_REQUEST
```

#### **2. Configure Cognito User Pool**
```bash
# Create user pool
aws cognito-idp create-user-pool --pool-name MyCognitoUsersPool

# Create user groups
aws cognito-idp create-group --group-name Users --user-pool-id <USER_POOL_ID>
aws cognito-idp create-group --group-name Admin --user-pool-id <USER_POOL_ID>
```

#### **3. Create S3 Bucket**
```bash
aws s3 mb s3://your-hotel-bucket
aws s3 website s3://your-hotel-bucket --index-document index.html --error-document 404.html
```

#### **4. Deploy Lambda Functions**
Deploy all 12 Lambda functions via AWS Console or Infrastructure as Code (SAM/CDK).

#### **5. Configure API Gateway**
Create REST API and link endpoints to Lambda functions.

### **Frontend Configuration**

Update `js/login.js`:
```javascript
const cognitoDomainPrefix = "your-domain-prefix";
const clientId = "your-client-id";
const redirectUri = "https://your-bucket.s3.amazonaws.com/index.html";
```

Update API configuration in JS files:
```javascript
const apiId = "your-api-id";
const apiURL = `https://${apiId}.execute-api.us-east-1.amazonaws.com/dev`;
```

### **Deploy to S3**
```bash
aws s3 sync . s3://your-hotel-bucket/ --exclude ".git/*" --exclude ".vscode/*"
```

### **Local Development**
```bash
python -m http.server 8000
# OR
npx http-server -p 8000
```

Access at: `http://localhost:8000`

## User Features

### **Guest Experience Flow**

**1. Landing Page** - Hero carousel, featured rooms, attractions, testimonials
**2. Authentication** - Cognito OAuth login with JWT tokens
**3. Room Search** - Flatpickr date picker, guest count selector (1-25)
**4. Room Catalog** - Filtered rooms with images, pricing, amenities
**5. Booking Process** - Pre-filled form with user details, validation
**6. My Bookings** - View all bookings with status badges (Pending/Approved/Denied)

### **Features**
- Smart date validation (check-out > check-in, dates in future)
- Guest count filtering (MaxGuests >= selected)
- Custom alerts replacing browser popups
- Loading spinner during API calls
- Responsive design for all devices

## Admin Dashboard

### **Access Control**
- Admin users assigned to "Admin" group in Cognito
- JWT token includes `cognito:groups` claim
- `adminAuthenticator.js` verifies admin status
- Unauthorized users redirected to homepage

### **Admin Features**

#### **1. Booking Management Tab**
- View all bookings in sortable table
- Approve/Deny buttons for pending bookings
- Real-time status updates without page reload
- Color-coded rows (green=approved, red=denied)
- Automatic room availability updates

#### **2. Room Management Tab**
- View all rooms with images and details
- Add new rooms via modal form
- Edit existing room details
- Delete rooms with confirmation
- Upload room images to S3
- Form validation (price > 0, guests 1-25)

## Security

### **Authentication & Authorization**
- **Cognito OAuth 2.0**: Secure login with JWT tokens
- **Password Policy**: Min 8 chars, uppercase/lowercase/numbers/symbols
- **Group-Based Access**: Lambda authorizers validate admin permissions
- **Token Expiration**: 1 hour access tokens, 30 days refresh tokens

### **Data Security**
- **Encryption at Rest**: DynamoDB AES-256 encryption
- **Encryption in Transit**: TLS 1.2+ for all connections
- **IAM Policies**: Least privilege access for Lambda
- **Input Validation**: Frontend and backend validation

### **API Security**
- CORS configuration limiting origins
- Rate limiting (1000 req/s)
- Request validation
- Lambda authorizers for protected endpoints

## Future Enhancements

### **Planned Features**
- Payment gateway integration (Stripe/PayPal)
- Email notifications (SES)
- CloudFront CDN for global performance
- Multi-language support
- Guest reviews and ratings
- Mobile app (React Native)
- Advanced analytics dashboard
- Dynamic pricing engine
- Chatbot support (Lex)

### **Technical Improvements**
- Infrastructure as Code (Terraform/CloudFormation)
- CI/CD pipeline (CodePipeline/GitHub Actions)
- Automated testing (Jest, Cypress)
- GraphQL API (AppSync)
- WebSocket for real-time updates
- Machine learning for demand forecasting

## Contributing

This project was developed as part of a cloud computing course focusing on AWS serverless architecture. Contributions are welcome!

### **How to Contribute**
1. Fork the repository
2. Create feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open Pull Request

### **Contribution Guidelines**
- Follow existing code style (camelCase for JS)
- Add comments for complex logic
- Test on multiple browsers
- Update documentation
- Verify admin functionality

## License

### **Educational Project**
This project was developed as part of a cloud computing and serverless architecture course, demonstrating:
- AWS services integration and orchestration
- Serverless architecture design patterns
- Modern web application development
- RESTful API design
- Security best practices for cloud applications
- Scalable and cost-effective infrastructure

### **Usage Rights**
Open source for educational purposes. Feel free to study, modify, and deploy with proper attribution.

### **External Services**
- **AWS**: Trademarks owned by Amazon Web Services, Inc.
- **Bootstrap, jQuery, Font Awesome**: Licensed under MIT/respective licenses

---

## Project Highlights

### **Technical Achievements**
- **100% Serverless**: Zero server management, automatic scaling
- **AWS Native**: Built entirely with AWS managed services
- **Secure**: Cognito OAuth with JWT, IAM policies, encryption
- **Real-time**: Instant UI updates without page refresh
- **Responsive**: Mobile-first design with Bootstrap 5
- **Comprehensive Admin**: Full CRUD operations
- **Automated Monitoring**: CloudWatch metrics with EventBridge

### **Learning Outcomes**
- AWS service integration (S3, Lambda, DynamoDB, Cognito, API Gateway)
- Serverless architecture design
- RESTful API development
- Frontend development (HTML/CSS/JavaScript)
- Authentication and authorization
- NoSQL database design
- Cloud security best practices

---

**Ready to explore serverless hotel booking? Clone, configure, and deploy today!**

**Version**: 1.0.0 | **Status**: Educational Project | **Last Updated**: January 2026

Built with ☁️ by Roee using AWS Cloud Services

---

> 📚 **Educational Purpose**
This project was created as part of a cloud computing course to demonstrate serverless architecture and AWS services integration.
