# Class Attendance App (MMA)

A mobile-friendly web app for a martial arts class (Monday and Wednesday, 8pm to 10pm). Students check in by scanning a fixed QR code, earn points, and can redeem rewards. Coaches record payments (Cash, Bank-in, TNG), sell drinks, manage monthly plans and monitor attendance, stock and collections.

- **Hosting:** GitHub Pages (single static file, `index.html`)
- **Backend:** Supabase (database, login, privacy rules)
- **Status:** in test mode. Test data should be cleared and test mode switched off before real classes.

## Main features

**Students**
- Register once (phone number plus a 6-digit PIN), log in again with the same details
- Check in by scanning the class QR code (days and times enforced unless test mode is on)
- Points per class (10), points held until the coach records payment
- Rewards: free drink, RM 5 off, guest pass, free class, merchandise (coach can edit rewards)
- Leaderboard pop-up (press and hold the points area)
- Buy drinks (water, soda) by scanning the drink shop QR code
- Payment pending or paid bar, monthly plan status
- Self-update of details (My details), including photo, emergency contact, health declaration and PIN
- English and Malay wording on forms

**Coaches**
- Attendance list by date, mark present or undo, record payments, view contact, emergency and health details
- Collection totals (daily, monthly, all time) split by class fees, monthly plans, drinks and by Cash, Bank-in, TNG
- Outstanding balances (class fees and drinks)
- Monthly plans (RM 120, points capped at 60 per month)
- Drink shop: sales, payments, stock levels, end-of-day count
- Reward requests and guest passes, bonus points (capped at 30 per month per student), automatic referral points
- Coach team: invite code registration, several coaches can monitor
- Class name, logo, printable A5 QR sheets with a customisable message
- All coach sections collapse and expand

**Master admin (one person)**
- Cannot be removed by other coaches. Can hand over the role to another coach
- Reset coach passwords, remove coaches, see the invite code
- Test mode switch and test-data deletion
- Delete a student (payment history is kept)

## Version history

### Stage 1: browser-only demo (single HTML file, data kept in one browser)
Used to design and test the flow before adding a database.

1. **Core demo.** Coach and student views, one fixed QR per class with the date detected by the app, 10 points per check-in, payment recording (Cash, Bank-in, TNG), daily collection summary, CSV export
2. **Class settings.** Default fee RM 20, class time 8pm to 10pm, check-in open until 9:30pm
3. **Registration form.** Email, first and last name, contact number, address, emergency contact, health declaration. Coach can tap to call
4. **Branding and coach summary.** Logo upload and a summary card when the coach opens the page. "Teacher" renamed to "Coach"
5. **Student payment bar.** Paid or pending status shown next to points. Student photo upload
6. **Points and rewards.** Reward tiers, redemption with coach approval, bonus points, collapsible rewards. Points only count after payment is recorded
7. **Drink shop.** Water and soda at RM 2, second QR code, stock levels, add stock, end-of-day count with shortage check
8. **Reward actions.** Free drink reduces stock, RM 5 off adjusts the fee, free class records a RM 0 payment, guest pass with a code. Outstanding totals for class fees and drinks
9. **Monthly plan.** RM 120 per month, classes covered, points capped at 60 per month. Collection by source and method
10. **Leaderboard pop-up.** Press and hold the points area to view

### Stage 2: Supabase versions (shared database, real logins)

| Version | What changed |
|---|---|
| **v1** | Supabase connected. Student login with phone number and PIN, coach login with email and password. Registration, check-in, payments and collection totals. Test mode. Privacy rules (students see only their own data) |
| **v2** | Monthly plans, drinks and stock, outstanding totals, collection by source, class and drink shop QR codes |
| **v3** | Health declaration table (18 conditions, Yes or No) plus "Others". Coach registration with an invite code, several coaches, coach details |
| **v4** | Logo and class name editing, student and coach photos, mandatory review of the new health declaration, "My details" self-update |
| **v5** | Rewards with approval (drink, discount, free class, guest pass), bonus points, leaderboard pop-up, automatic referral points by the friend's phone number, points settings |
| **v6** | Malay wording on forms. New "Please State" fields: other illnesses, operation or surgical, allergic, family medical history, disability or handicap. Everyone re-confirms their details |
| **v7** | Forgot password and PIN (coach email reset link, coach resets student PIN, self change). Health table redesigned for mobile (English above Malay). Details arrow below the student name. Master admin role. Test data deletion |
| **v8** | Coach contact number beside the name, buttons on the next line. Printable A5 QR sheets with a customisable message |
| **v9** | Registration form keeps what was typed and highlights missing fields |
| **v10** | QR codes at the top, every coach section collapsible, test mode and test data combined at the bottom (master admin only), Delete student keeps payment history, rewards shown in two tones (green when affordable, grey when not), version label at the bottom |

## Database overview (Supabase)

Tables: `settings`, `profiles`, `checkins`, `payments`, `plans`, `items`, `sales`, `stock_log`, `bonus`, `rewards`, `redeems`, `coach_invite`

Functions: `check_in`, `my_points`, `earned_pts`, `leaderboard`, `buy_item`, `redeem_reward`, `decide_redeem`, `reset_login`, `transfer_master`, `clear_collection`, `delete_student`, `check_invite`, plus triggers that create profiles, award referral points and protect the master admin and test mode.

Row-level security is on for every table. Students can read only their own records, coaches can read everything, and sensitive actions (reset, delete, approve) check the caller's role inside the database.

## Setup

1. Create a Supabase project (Singapore region is closest to Malaysia)
2. Authentication, Sign In / Providers: turn **on** "Allow new users to sign up", turn **off** "Confirm email" (students log in with a phone-based address that has no inbox)
3. Authentication, URL Configuration: set the Site URL to your GitHub Pages address so password reset links work
4. Apply the database setup (tables, functions, privacy rules) in the SQL Editor
5. Create the first coach account under Authentication, Users, and mark it as coach and master admin
6. Put your project address and **publishable** key in the `createClient(...)` line at the top of the script in `index.html`
7. Upload `index.html` to a GitHub repository and turn on GitHub Pages (Settings, Pages, branch `main`, folder `/root`)

## Security notes

- The publishable key in `index.html` is meant to be public. Protection comes from the privacy rules in the database
- **Never** put the Supabase secret or `service_role` key in the app, in GitHub or in chat
- Keep leaked-password protection off (it would reject simple 6-digit PINs)
- Health, address and emergency details are sensitive personal data. Collect only what is needed, keep the consent statement on the registration form, and limit coach access to people you trust
- Protect GitHub and Supabase logins with two-step verification
- Payment records are normally kept for several years for accounting. Check with your accountant before deleting any

## Known limits and next steps

- Test mode is still on and sample data exists. Clear test data and switch test mode off before launch
- The login boxes still redraw after a wrong PIN (the phone number must be retyped)
- Supabase's built-in email service has a low sending limit. Set up a custom email service before relying on password reset emails
- Photos and logos are stored as small images inside the database
- Referral points are given on registration, so fake sign-ups are possible. The coach can remove suspicious referral bonuses
- Optional ideas: dummy test student excluded from totals, receipt photos, monthly sales report and export, announcements board, SMS or WhatsApp reminders
- Database setup was applied through Supabase migrations. Consider exporting the schema into this repository as a `.sql` file for backup
