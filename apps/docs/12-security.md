# Security & Privacy

## Authentication

- **Provider**: Supabase Auth (email + password)
- **Password requirements**: Minimum 8 characters, 1 uppercase, 1 number
- **Session persistence**: JWT with refresh tokens
- **Mobile storage**: SecureStore (iOS Keychain)
- **Web storage**: HttpOnly cookies
- **Session expiry**: 7 days (mobile), 1 day (web), configurable per role

## Authorization (Row-Level Security)

PostgreSQL Row-Level Security (RLS) policies enforce data isolation:

```sql
-- Tenants can only access their own data
CREATE POLICY tenant_isolation ON tickets
  FOR ALL
  USING (tenant_id = (current_setting('app.current_tenant_id', true))::uuid);

-- Front desk staff can only see their outlet's tickets
CREATE POLICY front_desk_outlet ON tickets
  FOR ALL
  USING (
    (current_setting('app.current_role', true) = 'front_desk' 
     AND outlet_id = (current_setting('app.current_outlet_id', true))::uuid)
    OR current_setting('app.current_role', true) IN ('owner', 'manager', 'technician')
  );

-- Technicians can update tickets assigned to them or unassigned
CREATE POLICY technician_update ON tickets
  FOR UPDATE
  USING (
    current_setting('app.current_role', true) = 'technician'
    AND (technician_id = auth.uid() OR technician_id IS NULL)
    AND status IN ('received', 'assessing')
  );
```

## API Security

- **HTTPS only**: All endpoints enforce TLS 1.3
- **CORS**: Strict origin whitelist (mobile app, web dashboard)
- **Rate limiting**: 
  - 100 req/min per IP address
  - 1000 req/min per authenticated user
  - 10 req/min for `/api/ai/*` endpoints (cost control)
- **Input validation**: Zod schemas on every endpoint
- **SQL injection**: Drizzle ORM uses parameterized queries
- **File uploads**: 
  - Max 5MB per file
  - Allowed types: jpg, jpeg, png, webp
  - Stripped EXIF data on upload (privacy)

## Data Encryption

| Layer | Method |
|---|---|
| **At rest (Database)** | AES-256 (Supabase managed) |
| **At rest (Files)** | AES-256 (Supabase Storage) |
| **In transit** | TLS 1.3 (all API + database connections) |
| **Passwords** | bcrypt (Supabase Auth managed) |
| **Secrets (API keys)** | Environment variables, never in code or client |

## Secrets Management

- **OpenAI API key**: `OPENAI_API_KEY` env var, never exposed to client
- **WhatsApp API token**: `WHATSAPP_API_KEY` env var, server-side only
- **Supabase keys**: `SUPABASE_SERVICE_ROLE_KEY` server-side only; `SUPABASE_ANON_KEY` for client
- **Database URL**: `DATABASE_URL` env var, connection pooling with PgBouncer

## Malaysia PDPA (Personal Data Protection Act 2010)

| Principle | Implementation |
|---|---|
| **Consent** | Implicit consent when customer provides phone for repair notification. Explicit consent checkbox for marketing (future). |
| **Purpose limitation** | Data used only for repair communication and business records. No data sharing with third parties. |
| **Data minimization** | Only collect: name, phone, email (optional). No NRIC, address, or payment info. |
| **Retention** | Active customer data: 2 years from last ticket. Anonymized after: replace name, mask phone. |
| **Access request** | Customer can request their data via WhatsApp. Admin can export as JSON from dashboard. |
| **Correction** | Staff can update customer info from ticket screen. History preserved. |
| **Breach notification** | If >1000 records affected, notify PDPA Commissioner within 72 hours. System auto-generates breach report. |

## Incident Response

1. **Detect**: Monitoring alerts on abnormal access patterns
2. **Contain**: One-click revoke all sessions from admin dashboard
3. **Investigate**: Audit logs, ticket_history, server logs
4. **Notify**: PDPA authority if required; affected customers via WhatsApp
5. **Fix**: Patch, rotate keys, code review
6. **Post-mortem**: Document, update security measures

## Production Security Checklist

- [ ] All RLS policies tested with different role accounts
- [ ] Input validation on every endpoint (no raw SQL, no unsanitized user input)
- [ ] API keys rotated from development to production
- [ ] SSL enforced (HSTS header)
- [ ] CORS restricted to known origins
- [ ] Rate limiting tested
- [ ] File upload restrictions enforced
- [ ] Session timeout configured
- [ ] Password strength enforced
- [ ] No secrets in client code (audit via `git log -p`)
- [ ] Dependencies audited (`pnpm audit`)
- [ ] Backup strategy verified
