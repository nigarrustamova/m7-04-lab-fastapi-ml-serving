# Architectural Decisions

## 1. Versioning
I chose path-based versioning (`/v1/`) because it is the most discoverable and explicit way for consumers to manage breaking changes. It also simplifies caching and routing logic at the API gateway level compared to header-based versioning.

## 2. Batch Ordering and Partial Failures
The `predict-batch` endpoint uses an object keyed by caller-supplied IDs to ensure results can be mapped back to inputs regardless of processing order. In case of partial failures (e.g., one corrupt image), the API returns a 200 OK with the specific error object nested under the corresponding ID, allowing clients to recover and process the successful parts of the batch without failing the entire request.

## 3. Async Lifecycle
Jobs follow a `queued → running → completed → failed` state machine. Results are retained for 24 hours to allow consumers sufficient time to poll and retrieve their predictions before the data is purged for privacy and storage efficiency.
