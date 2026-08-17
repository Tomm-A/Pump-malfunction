# Pump-malfunction
This paper focuses on a water pump, and more precisely on the identification of hydraulic anomalies in the system where it is located. To pursue this objective, a set of data containing values measured by different sensors over a certain period of time is used.

## 1. Input

The dataset contains data from 52 sensors for different timestamps and the corresponding status of the pump. In particular, it is a table with:
- 55 columns, including a timestamp, the 52 sensors, the pump status, and a remaining column to be analyzed;
- 220320 rows, each of which is a recording at a given time.

This dataset is extracted from the Kaggle page https://www.kaggle.com/datasets/nphantawee/pump-sensor-data/data, and originated from the paper "K-Nearest Neighbors for Anomaly Detection and Predictive Maintenance in Water Pumping System" by João Pablo Santos da Silva et al.s

## 2. Output

The output to be considered is the column "machine status", whose value is one of the following:
- __NORMAL__, corresponding to the normal operation of the pump. This status corresponds to 205,836 records, 93.4% of the total;
- __BROKEN__, corresponding to when a malfunction takes place. Over the considered time span, the number of detected failures is 7, and they took place on the following dates:
    - 12/04/2018 21:55;
    - 18/04/2018 00:30;
    - 19/05/2018 03:18;
    - 25/05/2018 00:30;
    - 28/06/2018 22:00;
    - 08/07/2018 00:11;
    - 25/07/2018 14:00.
- __RECOVERING__, corresponding to when the pump is under repair and, therefore, it is always after a BROKEN status. It has 14,477 records, corresponding to 6.57% of the total.

## Modelling
Autoencoder is used in this paper. In this case, a model is trained to reconstruct the NORMAL operating status of the pump. When the predictions differ from the measured values, it implies that something anomalous is taking place. This is achieved through the following procedure:
- A model is trained only with data related to the NORMAL status;
- The trained model is used to make predictions, and the actual output evaluated is the Mean Squared Error (MSE) compared to the ground-truth data. When the MSE exceeds a defined threshold, an anomaly is detected.

The Autoencoder is based on deep neural networks, a framework based on the interaction of digital neurons that process input data to return a reconstructed output.

<img width="422" height="161" alt="image" src="https://github.com/user-attachments/assets/a0b8d177-76d8-406c-8061-aefeef75330c" />

Autoencoder can be also used to anticipate the occurrence of the BROKEN status. In the field, before a machine breakdown, the associated sensors usually show signs of decaying performance. Therefore, the objective is to detect these warning signs as early as possible, via a increasing error MSE of the prediction. An example is shown in the following plot.

<img width="422" height="169" alt="image" src="https://github.com/user-attachments/assets/4ae96a16-3c5c-4a75-84e9-45c6d6c11f22" />

Each red dot corresponds to a BROKEN status, which correspond to MSE peaks in the plot below.
The Autoencoder approach turned out to be the most effective method for correlating sensor measurements with the pump status. The trained model can be used for the following purposes:
- __Operational Status Monitoring__, to identify when the pump is malfunctioning or offline, serving as a reliable status indicator when direct telemetry is unavailable. This is particularly valuable in complex and distributed hydraulic networks to help pinpoint the root cause of anomalies detected by sensors;
- __Predictive Maintenance__, To anticipate pump failures before they occur. If deviations from nominal operating conditions become significant, a redundant/backup pump can be activated, allowing operators to shut down and inspect the primary unit. This significantly reduces maintenance costs and downtime by enabling timely intervention before an unexpected system shutdown occurs.

All analyses were conducted without prior domain knowledge of the specific sensor metrics, demonstrating the high flexibility and scalability of this approach to similar industrial systems. However, to ensure effectiveness, a sufficient amount of historical training data (representing normal operating conditions) must be available.
