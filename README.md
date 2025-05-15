import { useState } from 'react';

function BookingForm() {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    date: '',
    time: '',
  });

  const [idFile, setIdFile] = useState(null);
  const [message, setMessage] = useState('');
  const [isSubmitting, setIsSubmitting] = useState(false);

  const handleChange = (event) => {
    const { name, value } = event.target;
    setFormData((prevData) => ({ ...prevData, [name]: value }));
  };

  const handleFileChange = (event) => {
    const file = event.target.files[0];
    if (file && file.size > 2 * 1024 * 1024) { // Example: 2MB limit
      setMessage('File size must be less than 2MB.');
      setIdFile(null);
      return;
    }
    if (file && !['image/png', 'image/jpeg', 'application/pdf'].includes(file.type)) {
      setMessage('Please upload a valid ID in PNG, JPEG, or PDF format.');
      setIdFile(null);
      return;
    }
    setIdFile(file);
  };

  const handleSubmit = async (event) => {
    event.preventDefault();
    setIsSubmitting(true);

    if (!idFile) {
      setMessage('Please upload a valid ID for verification.');
      setIsSubmitting(false);
      return;
    }

    try {
      const formDataToSend = new FormData();
      formDataToSend.append('name', formData.name);
      formDataToSend.append('email', formData.email);
      formDataToSend.append('date', formData.date);
      formDataToSend.append('time', formData.time);
      formDataToSend.append('idFile', idFile);

      await new Promise((resolve) => setTimeout(resolve, 1000)); // Mock API call delay
      setMessage('Your appointment and ID verification have been successfully submitted!');
    } catch (error) {
      console.error(error);
      setMessage('Failed to book your appointment. Please try again.');
    } finally {
      setIsSubmitting(false);
    }
  };

  return (
    <form className="max-w-md mx-auto bg-white p-6 rounded shadow-md" onSubmit={handleSubmit}>
      <div className="mb-4">
        <label htmlFor="name" className="block text-gray-700 font-medium mb-2">
          Full Name
        </label>
        <input
          type="text"
          id="name"
          name="name"
          value={formData.name}
          onChange={handleChange}
          className="w-full border border-gray-300 rounded px-3 py-2"
          required
        />
      </div>

      <div className="mb-4">
        <label htmlFor="email" className="block text-gray-700 font-medium mb-2">
          Email Address
        </label>
        <input
          type="email"
          id="email"
          name="email"
          value={formData.email}
          onChange={handleChange}
          className="w-full border border-gray-300 rounded px-3 py-2"
          required
        />
      </div>

      <div className="mb-4">
        <label htmlFor="date" className="block text-gray-700 font-medium mb-2">
          Appointment Date
        </label>
        <input
          type="date"
          id="date"
          name="date"
          value={formData.date}
          onChange={handleChange}
          className="w-full border border-gray-300 rounded px-3 py-2"
          required
        />
      </div>

      <div className="mb-4">
        <label htmlFor="time" className="block text-gray-700 font-medium mb-2">
          Appointment Time
        </label>
        <input
          type="time"
          id="time"
          name="time"
          value={formData.time}
          onChange={handleChange}
          className="w-full border border-gray-300 rounded px-3 py-2"
          required
        />
      </div>

      <div className="mb-4">
        <label htmlFor="idFile" className="block text-gray-700 font-medium mb-2">
          Upload ID for Verification
        </label>
        <input
          type="file"
          id="idFile"
          name="idFile"
          onChange={handleFileChange}
          className="w-full border border-gray-300 rounded px-3 py-2"
          required
        />
      </div>

      {message && (
        <div
          className={`mb-4 p-2 text-white rounded ${
            message.includes('successfully') ? 'bg-green-500' : 'bg-red-500'
          }`}
          aria-live="polite"
        >
          {message}
        </div>
      )}

      <button
        type="submit"
        className={`w-full py-2 px-4 text-white rounded ${
          isSubmitting ? 'bg-gray-400' : 'bg-blue-500 hover:bg-blue-600 focus:ring-2 focus:ring-blue-300'
        }`}
        disabled={isSubmitting}
      >
        {isSubmitting ? 'Submitting...' : 'Book Appointment'}
      </button>
    </form>
  );
}

export default BookingForm;
