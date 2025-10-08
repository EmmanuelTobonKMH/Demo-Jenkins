# GitLab Secret Detection Test Patterns

**WARNING: These are test patterns only! Do not use real credentials or tokens.**

This repository contains test patterns for GitLab's secret detection feature, with patterns specifically crafted to match detection rules.

## Test Patterns

### GitLab Tokens

```text
# Personal Access Token (20 chars)
glpat-12345678901234567890

# Personal Access Token Routable (27+ chars + format)
glpat-123456789012345678901234567.ab1234567

# Pipeline Trigger Token (40 chars)
glptt-1234567890123456789012345678901234567890

# Runner Authentication Token (20 chars)
glrt-12345678901234567890

# OAuth Application Secret (64 chars)
gloas-1234567890123456789012345678901234567890123456789012345678901234

# Feed Token (20 chars)
glft-12345678901234567890

# Kubernetes Agent Token (50 chars)
glagent-12345678901234567890123456789012345678901234567890

# Incoming Email Token (25 chars)
glimt-1234567890123456789012345
```

### Cloud Provider Tokens

```text
# AWS Access Token (16 uppercase chars after AKIA)
AKIA1234567890ABCD

# GCP API Key (35 chars after AIza)
AIza1234567890123456789012345678901234x

# GCP Service Account
{"private_key": "-----BEGIN PRIVATE KEY-----\nMIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQC\n-----END PRIVATE KEY-----\n"}

# GCP OAuth Client Secret (28 chars after prefix)
GOCSPX-12345678901234567890123456
```

### GitHub Tokens

```text
# Personal Access Token (36 chars)
ghp_123456789012345678901234567890123456

# OAuth Access Token (36 chars)
gho_123456789012345678901234567890123456

# App Token (36 chars each format)
ghu_123456789012345678901234567890123456
ghs_123456789012345678901234567890123456

# Refresh Token (76 chars)
ghr_1234567890123456789012345678901234567890123456789012345678901234567890123456
```

### E-commerce & Payment Tokens

```text
# Shopify Shared Secret (32 hex chars)
shpss_1234567890abcdef1234567890abcdef

# Shopify Access Token (32 hex chars)
shpat_1234567890abcdef1234567890abcdef

# Shopify Custom App Access Token (32 hex chars)
shpca_1234567890abcdef1234567890abcdef

# Shopify Private App Access Token (32 hex chars)
shppa_1234567890abcdef1234567890abcdef

# Stripe Test Keys (21-32 chars after prefix)
sk_test_1234567890abcdef12345
pk_test_1234567890abcdef12345

# Stripe Live Keys (21-32 chars after prefix)
sk_live_1234567890abcdef12345
pk_live_1234567890abcdef12345
```

### Package Registry Tokens

```text
# PyPI Upload Token (exact prefix + 50+ chars)
pypi-AgEIcHlwaS5vcmc1234567890123456789012345678901234567890

# npm Access Token (36 chars)
"npm_123456789012345678901234567890123456"

# Rubygem API Token (48 hex chars)
rubygems_1234567890abcdef1234567890abcdef1234567890abcdef
```

### Communication & Monitoring Tokens

```text
# Slack Tokens (format: xox[baprs]-{10,48} chars)
xoxb-1234567890-123456789012-123456789012
xoxa-1234567890-123456789012-123456789012
xoxp-1234567890-123456789012-123456789012
xoxr-1234567890-123456789012-123456789012
xoxs-1234567890-123456789012-123456789012

# Grafana API Token (72-92 chars)
"eyJrIjoi1234567890123456789012345678901234567890123456789012345678901234567890xx"

# Mailchimp API Key (includes -us20 suffix)
mailchimp_key="1234567890abcdef1234567890abcdef-us20"

# Mailgun Private API Token
mailgun_key="key-1234567890abcdef1234567890abcdef"

# Mailgun Webhook Key
mailgun_signing="1234567890abcdef1234567890abcdef-12345678-12345678"

# New Relic User API Key (27 chars after NRAK-)
"NRAK-123456789012345678901234567"

# New Relic User API ID (64 chars)
newrelic_api="1234567890123456789012345678901234567890123456789012345678901234"
```

### Infrastructure Tokens

# Hashicorp Terraform Token (14 chars + atlasv1 + 60-70 chars)
"12345678901234.atlasv1.123456789012345678901234567890123456789012345678901234567890"

# Hashicorp Vault Token (156 chars after prefix)
b.AAAAAQ123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890

# Segment Public Token (64 chars)
sgp_12345678901234567890123456789012345678901234567890123456789012

## Testing Instructions

1. Create test files with individual patterns
2. Commit and push to trigger detection
3. Check Security Dashboard for results
4. Follow proper token format requirements:
   - Exact character counts
   - Case sensitivity
   - Word boundaries
   - Required prefixes/suffixes
   - Hex characters where specified
   - Quotes when required

## Important Notes

- All tokens are intentionally invalid
- Never use real credentials
- Remove test files after validation
- Some tokens require specific formatting (quotes, prefixes, etc.)
- Case sensitivity matters for many patterns
