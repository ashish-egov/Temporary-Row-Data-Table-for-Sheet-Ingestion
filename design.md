### ✅ **Temporary Row Data Table for Sheet Ingestion**

#### 📌 Table Name: `eg_cm_campaign_data`

| Column Name            | Description                                                |
| ---------------------- | ---------------------------------------------------------- |
| `campaignNumber`       | Identifier for the campaign (e.g., "Campaign2025Q1")       |
| `type`                 | Type of data (e.g., "user", "facility", etc.)           |
| `uniqueIdentifier`     | A unique value used to identify rows (e.g., Aadhaar, ID)   |
| `data` (JSONB)         | Full row data from the sheet in JSON format                |
| `uniqueIdAfterProcess` | Unique ID from DB after processing (null if not yet saved) |
| `status`               | Status of processing: `pending`, `processed`               |

---

### 🔄 **Flow: Sheet Upload to DB Processing**

1. User uploads a sheet containing user data.
2. Each row is temporarily saved in the `eg_cm_campaign_data` table.
3. For each row:

   * Extract `uniqueIdentifier`.
   * Check in DB if a user with the same `uniqueIdentifier` exists.
4. If **user does not exist**:

   * Add the row to a list.
   * Persist it in the database.

---

### 🔁 **Boundary Change Handling**

#### 📌 Table Name: `sheet_boundary_mapping`

| Column Name        | Description                                                           |
| ------------------ | --------------------------------------------------------------------- |
| `campaignNumber`   | Same as above                                                         |
| `type`             | Same as above                                                         |
| `uniqueIdentifier` | Same as above                                                         |
| `boundary`         | New or changed boundary value                                         |
| `mappingId`        | ID for the mapping relationship (can be UUID or generated value)      |
| `status`           | Status of mapping: `mapped`, `demapped`, `tobemapped`, `tobedemapped` |

---

### 🔄 **Flow: Boundary Change Handling**

1. When a boundary change is detected for a user/row:

   * Record it in `sheet_boundary_mapping`.
   * Mark appropriate status based on the operation:

     * `tobemapped` – new mapping to be applied.
     * `tobedemapped` – existing mapping to be removed.
     * `mapped` – mapping applied.
     * `demapped` – mapping removed.

---
