# MonitorMW-Client

This repo hold example notebooks for accessing data stored on [Monitor My Watershed](https://monitormywatershed.org/) through the time series visualization (TSV) endpoint using both R and python.
The TSV endpoint was **not** intended to be an external data access point or API, but can be used for that purpose.

The example notebooks in this repository were originally hosted in two [GitHub Gists under Sara Damiano's account](https://gist.github.com/SRGDamia1).

- [MonitorMW-Client](#monitormw-client)
  - [Downloading data from Monitor My Watershed using the TSV Endpoint](#downloading-data-from-monitor-my-watershed-using-the-tsv-endpoint)
    - [First Post Request](#first-post-request)
    - [Second Post Request](#second-post-request)

## Downloading data from Monitor My Watershed using the TSV Endpoint

This notebook demonstrates how to use the Time Series Visualization (TSV) endpoint to extract data from Monitor My Watershed.
This endpoint serves as a temporary access point between the shut-down of the WoFpy service and the development of a more efficient and permanent service.

To get data from Monitor My Watershed, we need to make 2 post requests to <https://monitormywatershed.org/dataloader/ajax/>.
The post is sent as form data, sending the key `request_data` with a JSON object as the value.
In the JSON object is the method and other request data.

### First Post Request

- In the first POST request, we specify specifying the method `get_sampling_feature_metadata` and the sampling feature code:

  - The JSON looks like this:

    ```json
    {
      "method": "get_sampling_feature_metadata",
      "sampling_feature_code": "YOUR_SITE_CODE"
    }
    ```

  - The full request looks like this (in CURL)

    ```sh
        curl --location 'https://monitormywatershed.org/dataloader/ajax/' \
             --form 'request_data="{\"method\": \"get_sampling_feature_metadata\",\"sampling_feature_code\": \"YOUR_SITE_CODE\"}"'
    ```

  - The response from this first POST request is a JSON containing a list of results available for the sampling feature.
    By parsing the JSON, you can get the result ID for the UUID or variable code that you're interested in.

### Second Post Request

- In the second post request we specify specifying the method `get_result_timeseries`, the result ID and the date range (if desired):
  - If you want the entire range of data, you do not need to specify the start and end date. - _If you request the entire date range, the response may take a **LONG** time to appear!!_ - NOTE: The result ID is an arbitrary number and **may change at any time**.
    You should get the correct result id each time using the first POST request.
  - The JSON looks like this:

    ```json
    {
      "method": "get_result_timeseries",
      "resultid": "RESULT_NUMBER_FROM_FIRST_REQUEST",
      "start_date": "2024-03-01T00:00:00-05:00",
      "end_date": "2024-03-15T00:00:00-05:00"
    }
    ```

  - The full request looks like this (in CURL)

    ```sh
      curl --location 'https://monitormywatershed.org/dataloader/ajax/' \
           --header 'origin: https://monitormywatershed.org' \
           --header 'Referer: https://monitormywatershed.org/tsv/' \
           --form 'request_data="{\"method\": \"get_result_timeseries\", \"resultid\": \"563\", \"start_date\": \"2024-03-01T00:00:00-05:00\", \"end_date\": \"2024-03-15T00:00:00-05:00\"}"'
    ```

  - The response from this second POST request is a JSON containing a lists of value id's, data values, timestamps (as a unix time - the number of seconds from January 1, 1970), and the UTC offset of the data.
    This JSON can be parsed into a data frame or any format you want.

<!-- cSpell:words  resultid -->
