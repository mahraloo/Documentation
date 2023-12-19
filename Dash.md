
# Sharing Data Between Callbacks

- Dash was designed to be a stateless framework
    - stateless has no memory or knowledge of previous calls or requests


## Share State

In some apps, you may have multiple callbacks that depend on expensive data processing tasks like making database queries, running simulations, or downloading data.

Rather than have each callback run the same expensive task, you can have one callback run the task and then share the results to the other callbacks.

## Global Variabled in One Callbacks.
When Dash apps run across multiple workers, their memory is not shared. This means that if you modify a global variable in one callback, that modification will not be applied to the other workers / processes.

## Storing Shared Data
To share data safely across multiple processes or servers, we need to store the data somewhere that is accessible to each of the processes.

There are three places you can store this data:

- In the user's browser session, using [dcc.Store](https://dash.plotly.com/dash-core-components/store)
- On the disk (e.g in a file or database)
- In server-side memory(RAM) shared across processes such as a Redis database.

### Example 1 - Storing Data in the Browser with dcc.Store
To save data in the user's browser's session:
- The data has to be converted to a string like JSON or base64 encoded binary data for storage
- Data that is cached in this way will only be available in the user's current session
    - If you open up a new browser window, the app's callbacks will always re-compute the data. The data is only cached between callbacks within the same session.
    - This method doesn't increase the memory footprint of the app.
    - There could be a cost in network traffic. If you're sharing 10MB of data between callbacks, then that data will be transported over the network between each callback.
    - If the network cost is too high, then compute the aggregations upfront and transport those. Your app likely won't be displaying 10MB of data, it will just be displaying a subset or an aggregation of it
### Example 2 - Computing Aggregations Upfront
Sending the computed data over the network can be expensive if the data is large. In some cases, serializing this data to JSON can also be expensive.

In many cases, your app will only display a subset or an aggregation of the processed data. In these cases, you could precompute the aggregations in your data processing callback and transport these aggregations to the remaining callbacks.

### Example 3 - Caching and Signaling
- Uses Redis via Flask-Cache for storing “global variables” on the server-side in a database. This data is accessed through a function (global_store()), the output of which is cached and keyed by its input arguments.
- Uses the dcc.Store solution to send a signal to the other callbacks when the expensive computation is complete.
- Note that instead of Redis, you could also save this to the file system
- This “signaling” is performant because it allows the expensive computation to only take up one process and be performed once. Without this type of signaling, each callback could end up computing the expensive computation in parallel, locking four processes instead of one.
Another benefit of this approach is that future sessions can use the pre-computed value. This will work well for apps that have a small number of inputs.
- Example 4 - User-Based Session Data on the Server
    The previous example cached computations in a way that was accessible for all users.

    Sometimes you may want to keep the data isolated to user sessions: one user's derived data shouldn't update the next user's derived data. One way to do this is to save the data in a dcc.Store, as demonstrated in the first example.

    Another way to do this is to save the data in a cache along with a session ID and then reference the data using that session ID. Because data is saved on the server instead of transported over the network, this method is generally faster than the dcc.Store method.


## Requirements

 - [Redis](https://redis.io/)
 - [Flask-cashing](https://flask-caching.readthedocs.io/en/latest/)
## 🚀 About Me
I'm a tech buff (:


## 🔗 Links
[![source](https://img.shields.io/badge/source-000?style=for-the-badge&logo=ko-fi&logoColor=white)](https://dash.plotly.com/sharing-data-between-callbacks)
[![linkedin](https://img.shields.io/badge/linkedin-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)]([www.linkedin.com/in/aminloo07](https://www.linkedin.com/in/aminloo07/)https://www.linkedin.com/in/aminloo07/)



