# Interviews-Scheduler-App
import React, { useState, useEffect } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Select, SelectItem } from "@/components/ui/select";
import { DatePicker } from "@/components/ui/datepicker";
import { useNavigate } from "react-router-dom";

const AdminInterviewPage = () => {
  const [participants, setParticipants] = useState([]);
  const [selectedParticipants, setSelectedParticipants] = useState([]);
  const [startTime, setStartTime] = useState(null);
  const [endTime, setEndTime] = useState(null);
  const [interviews, setInterviews] = useState([]);
  const navigate = useNavigate();

  useEffect(() => {
    // Fetch participants from API
    fetch("/api/participants")
      .then((res) => res.json())
      .then((data) => setParticipants(data));
  }, []);

  const createInterview = () => {
    if (!selectedParticipants.length || !startTime || !endTime) {
      alert("All fields are required");
      return;
    }

    if (new Date(startTime) >= new Date(endTime)) {
      alert("Start time must be before end time");
      return;
    }

    const newInterview = {
      id: Date.now(),
      participants: selectedParticipants,
      startTime,
      endTime,
    };

    setInterviews([...interviews, newInterview]);
    alert("Interview created successfully");
  };

  const editInterview = (id) => {
    navigate(`/edit-interview/${id}`);
  };

  return (
    <div className="p-4">
      <h1 className="text-xl font-bold mb-4">Create an Interview</h1>
      <Card className="mb-4">
        <CardContent>
          <div className="mb-4">
            <label className="block font-semibold mb-2">Select Participants</label>
            <Select
              multiple
              onChange={(values) => setSelectedParticipants(values)}
              value={selectedParticipants}
            >
              {participants.map((participant) => (
                <SelectItem key={participant.id} value={participant.id}>
                  {participant.name}
                </SelectItem>
              ))}
            </Select>
          </div>
          <div className="mb-4">
            <label className="block font-semibold mb-2">Start Time</label>
            <DatePicker
              selected={startTime}
              onChange={(date) => setStartTime(date)}
              showTimeSelect
              dateFormat="Pp"
            />
          </div>
          <div className="mb-4">
            <label className="block font-semibold mb-2">End Time</label>
            <DatePicker
              selected={endTime}
              onChange={(date) => setEndTime(date)}
              showTimeSelect
              dateFormat="Pp"
            />
          </div>
          <Button onClick={createInterview}>Create Interview</Button>
        </CardContent>
      </Card>

      <h2 className="text-xl font-bold mb-4">Upcoming Interviews</h2>
      <div>
        {interviews.map((interview) => (
          <Card key={interview.id} className="mb-2">
            <CardContent>
              <p><strong>Participants:</strong> {interview.participants.join(", ")}</p>
              <p><strong>Start Time:</strong> {new Date(interview.startTime).toLocaleString()}</p>
              <p><strong>End Time:</strong> {new Date(interview.endTime).toLocaleString()}</p>
              <Button onClick={() => editInterview(interview.id)}>Edit</Button>
            </CardContent>
          </Card>
        ))}
      </div>
    </div>
  );
};

export default AdminInterviewPage;
