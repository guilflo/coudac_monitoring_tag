# Coudac Monitoring Tag
This tag's purpose is to record any conversion event of your choice to Coudac database in BigQuery. This tag is greatly inspired from [sGTM Pantheon Chaos tag template](https://github.com/google-marketing-solutions/gps-sgtm-pantheon/blob/main/sgtm/chaos/README.md) and has been tailored to match Coudac specific analytics needs.

![CleanShot 2025-03-21 at 12 01 45](https://github.com/user-attachments/assets/8c605acb-ef5b-4079-9232-cf141d398db7)

When importing it to your server-side container, you must use the merge option of your choice. Remember that the tag needs both built-in variable *Client Name* and *Event Name* to function properly.

## Authentification: Sharing access from sGTM to BigQuery

As most of our sGTM setups use Addingwell GCP Projects and our database is one of our GCP project we need to share to Addingwell service account the right to write in one of our GCP Project. This is actually quite simple, just follow this two steps:
1. Go to your Server-Side container user settings and copy the AW service account email address

![CleanShot 2025-03-21 at 12 14 39](https://github.com/user-attachments/assets/08fcab1f-a022-45c7-9c15-3dec7176c35b)

2. Then go to IAM in the Coudac GCP project and add Big Query Data Editor right to AW service account email address

![CleanShot 2025-03-21 at 12 16 20](https://github.com/user-attachments/assets/54aca7da-2609-4e99-a047-d08efa84fb77)

This two simple steps will allow AW service account to access and write into our BigQuery monitoring database. If you have an 400 error when testing the tag, this is probably because you did not correctly implemented this step.

## Customize the tag to your needs

Then to take full advantage of the tag you can act on three aspects.

### 1. Modifications to implement in the Variables section:
- **Coudac Monitoring Tag - Client Name** specify here the exact naming convention of the customer. You'll need to use the exact same name in the Funnel sub-account of the customer.
- **Coudac Monitoring Tag - Event Filter**: by default this lookup table is set to record only two events type *purchase* and *generate_lead*. You can modify that filter to send others events. Please be aware that if you send lower funnel events (like *page_view*, *view_item_list* or *view_item*) you are going to record a lot of events which is then going to cost more in database fees.

![CleanShot 2025-03-21 at 12 04 19](https://github.com/user-attachments/assets/9676e755-bb78-44ec-9d63-128d9ae5d95d)

![CleanShot 2025-03-21 at 12 04 46](https://github.com/user-attachments/assets/d18070a2-bfed-4796-9328-eed0b609b69c)

### 2. Make sure conversion events parameters match your current setup:
- **User Data**: to record the estimated quality of events parameters, the tag sends both *email* and *phone_number* to the database in hash hex format (using Simo Ahava's hash variable template, which is included in this tag template as well). By default, the hashed values are taken from the usual GA4 user_data object from the Client Side container (*user_data.email* and *user_data.phone_number*). Remember that if you use a different scheme you'll need to update this. If you also sends data directly to the server container using webhooks, you can change the sha256 variables to hash the content of a lookup variable.
- **Standard GA4 e-commerce variables**: the tag also sends when available *value*, *transaction_id* and *currency*, using by default GA4 naming convention. Again if you use a different scheme, don't forget to update the tag to send your own variables.

### 3. Add other events parameters to the tag:
As BigQuery is a SQL database, if you want to add another event parameters to the monitoring database, you'll need to edit the database scheme to do so. Just go to the correct table in BigQuery and add a new column using the correct format (*string*, *float*, *integer*). If you don't know which one to use you can either use GTM Server-Side container preview to check the Variable Type or you can go for a string type which will most of the time be convertible in BigQuery.

Enjoy
