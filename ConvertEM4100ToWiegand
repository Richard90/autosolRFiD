using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Text;
using System.Windows.Forms;
using System.IO.Ports;

namespace ConvertEM4100ToWiegand
{
    public partial class FormMain : Form
    {
        private SerialPort _serialPort = new SerialPort();
        private String _rxString = "";

        public FormMain()
        {
            InitializeComponent();
        }

        private void Form1_Load(object sender, EventArgs e)
        {
            GetPorts();
            _serialPort.DataReceived += SerialPortDataReceived;
        }

        private void GetPorts()
        {
            CombPort.Items.Clear();
            String[] ports = SerialPort.GetPortNames();

            foreach (String port in ports)
            {
                CombPort.Items.Add(port);
            }

            if (CombPort.Items.Count > 0)
            {
                CombPort.SelectedIndex = 0;
                LabeStatus.Text = "Status: Disconnected";
                ButtConnect.Enabled = true;
            }
            else
            {
                LabeStatus.Text = "Status: Serial ports not found";
                ButtConnect.Enabled = false;
            }
        }

        private void ConnectPort()
        {
            if (_serialPort.IsOpen == false)
            {
                _serialPort.BaudRate = 9600;
                _serialPort.PortName = CombPort.Text;
                _serialPort.Open();

                ButtDisconnect.Enabled = true;
                ButtConnect.Enabled = false;
                ButtRefreshPort.Enabled = false;
                LabeStatus.Text = "Status: Connected";
            }
        }

        private void DisconnectPort()
        {
            if (_serialPort.IsOpen == true)
            {
                _serialPort.Close();

                ButtDisconnect.Enabled = false;
                ButtConnect.Enabled = true;
                ButtRefreshPort.Enabled = true;
                LabeStatus.Text = "Status: Disconnected";
            }
        }

        private void SerialPortDataReceived(object sender, SerialDataReceivedEventArgs e)
        {
            Char[] result = new Char[16];
            for (Int32 len = 0; len < result.Length; )
            {
                len += _serialPort.Read(result, len, result.Length - len);
            }

            _rxString = new String(result);
            this.Invoke(new EventHandler(RegisterLog));

        }

        private void RegisterLog(object sender, EventArgs e)
        {
            TextEM4100.AppendText(_rxString + Environment.NewLine);
            TextWiegand.AppendText(_rxString.Substring(0, 5)
                                   + ConvertEM4100ToWiegand(_rxString.Split(':')[1].Substring(0, 10))
                                   + "#" + Environment.NewLine);
        }

        private String ConvertEM4100ToWiegand(String pTag)
        {
            String binari = (Convert.ToString(Convert.ToInt32(pTag), 2)).PadLeft(32, '0');
            return (Convert.ToString(Convert.ToInt32(binari.Substring(0, 16), 2)) +
                    Convert.ToString(Convert.ToInt32(binari.Substring(16, 16), 2))).PadLeft(10, '0');

        }
        
        private void ButtConnect_Click(object sender, EventArgs e)
        {
            ConnectPort();
        }

        private void ButtDisconnect_Click(object sender, EventArgs e)
        {
            DisconnectPort();
        }

        private void ButtClearEM4100_Click(object sender, EventArgs e)
        {
            TextEM4100.Clear();
        }

        private void ButtClearWiegand_Click(object sender, EventArgs e)
        {
            TextWiegand.Clear();
        }

        private void ButtRefreshPort_Click(object sender, EventArgs e)
        {
            GetPorts();
        }

        private void FormMain_FormClosing(object sender, FormClosingEventArgs e)
        {
            DisconnectPort();
        }
    }
}
