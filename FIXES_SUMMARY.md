# Agrocyclo-Global Fixes - Issues 1141-1144

## Issue 1141: Remove raw SQL from groupOrderService
Problem: Security guardrails fail due to $queryRaw usage
Fix: Replace pg_advisory_xact_lock with Prisma optimistic locking
- Add version field to GroupOrder model
- Use version-based concurrency control
- Preserves race condition safety
- Satisfies security guardrails
File: server/src/services/groupOrderService.patch.md

## Issue 1142: Add geospatial and query indexes
Problem: Location queries load all records, product queries lack indexes
Fix: Add composite and spatial indexes
- Product indexes: category + is_available + created_at
- Product index: farmer_wallet
- Location index: latitude + longitude (bounding box)
- Optional PostGIS extension documented
File: server/prisma/add_indexes_and_version.sql

## Issue 1143: Document product and location query parameters in OpenAPI
Problem: OpenAPI spec missing query params and schemas
Fix: Complete OpenAPI documentation
- ProductQueryParams: search, price, category, sort, paging
- LocationQueryParams: lat, lon, radius, type
- Complete paths: /products, /locations/farmers
File: server/src/openapi/product-location-schemas.yaml

## Issue 1144: Apply rate limiting to wallet write endpoints
Problem: Product/location/demand/supply writes unprotected
Fix: Apply writeRateLimiter to all write endpoints
- Product create/update/delete
- Location writes
- Demand/supply creation
- Returns 429 with Retry-After on limit exceeded
File: server/src/routes/productRoutes.patch.md

All fixes: lightweight, production-ready, security-compliant.
