
# Wabista Business Platform - Broadcast System Guide

This document provides a summary and user guide for the newly implemented broadcast system.

## System Overview

The broadcast system allows businesses to send high-volume messages to their customers using the Meta WhatsApp Business API standards. It supports campaign management, audience targeting, and real-time analytics.

### Key Features Implemented

1.  **Broadcast Wizard**: A step-by-step interface for creating campaigns.
    *   **Audience Selection**: Send to all database contacts, filter by tags (e.g., VIP, New Leads), or upload a custom CSV file for one-time lists.
    *   **Template Management**: Forces compliance by using pre-approved Meta templates.
    *   **Variable Substitution**: Dynamic content injection (e.g., `{{1}}` becomes "John") based on user input.
    *   **Preview**: Live preview of the message before sending.

2.  **CSV File Import for Broadcasts**:
    *   Supports ad-hoc lists via CSV.
    *   **Validation**: Enforces 91 (India) country code prefix and 12-digit length check.
    *   **Error Handling**: Skips invalid rows and provides a summary count.

3.  **Real-Time Analytics Dashboard**:
    *   **Live Status**: Tracks Sent, Delivered, Read, and Failed counts.
    *   **Visual Charts**: Pie charts for delivery distribution.
    *   **Failure Analysis**: Detailed list of failed numbers with error messages.
    *   **Retry Mechanism**: One-click retry for failed messages (simulated).

4.  **Meta API Compliance**:
    *   **Template-Only Sending**: To prevent spam blocking, broadcasts are restricted to templates.
    *   **Rate Limiting**: The system processes messages in batches (size: 50) with delays to respect API limits.
    *   **Status Webhooks**: The architecture supports status updates (sent/delivered/read) reflected in the UI.

## Database Updates

The following changes were made to the Supabase schema to support these features:

*   **New Table**: `broadcast_recipients`
    *   Stores individual message status for every recipient in a broadcast.
    *   Columns: `broadcast_id`, `phone_number`, `status` (sent/delivered/read/failed), `error_message`.
*   **Table Update**: `contacts`
    *   Added `tags` array column to support audience filtering.
*   **Security**:
    *   Row Level Security (RLS) policies added to ensure users can only access their own campaign data.

## How to Use the New System

### 1. Creating a Broadcast
1.  Navigate to the **Broadcasts** page from the sidebar.
2.  Click **New Broadcast**.
3.  **Step 1 (Details)**: Enter a campaign name and select your audience.
    *   *Option A*: Select "Existing Contacts" and optionally filter by a tag (e.g., VIP).
    *   *Option B*: Select "Upload CSV" to use a specific file. The CSV must have `name` and `number` headers. Numbers must start with `91`.
4.  **Step 2 (Template)**: Choose a message template from the approved list.
5.  **Step 3 (Review)**: Fill in any variables (if the template has `{{1}}` placeholders) and review the preview.
6.  Click **Start Broadcast**. Do not close the window until the progress bar reaches 100%.

### 2. Monitoring & Analytics
1.  On the Broadcasts list, click **View Analytics** next to any campaign.
2.  **Overview Tab**: View success rates and charts.
3.  **Logs Tab**: See a row-by-row breakdown of every recipient's status.
4.  **Retrying**: If there are failed messages, click the "Retry Failed" button in the Failure Analysis card to re-queue them.

## Technical Notes
- **Progress Tracking**: The sending process uses a client-side batching loop to update the UI progress bar in real-time.
- **Data Retention**: Broadcast logs are stored permanently in `broadcast_recipients` for historical auditing.
